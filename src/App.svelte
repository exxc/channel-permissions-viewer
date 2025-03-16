<script lang="ts">
  const ignoreProtoField = <T extends {}>(v: T): Omit<T, "__proto__"> => v;
  const DiscordChannelPermissions = Object.freeze(
    ignoreProtoField({
      __proto__: null,
      ViewChannel: 1024n,
      ManageChannels: 16n,
      ManageRoles: 268435456n,
      ManageWebhooks: 536870912n,
      CreateInstantInvite: 1n,
      SendMessages: 2048n,
      SendMessagesInThreads: 274877906944n,
      CreatePublicThreads: 34359738368n,
      CreatePrivateThreads: 68719476736n,
      EmbedLinks: 16384n,
      AttachFiles: 32768n,
      AddReactions: 64n,
      UseExternalEmojis: 262144n,
      UseExternalStickers: 137438953472n,
      MentionEveryone: 131072n,
      ManageMessages: 8192n,
      ManageThreads: 17179869184n,
      ReadMessageHistory: 65536n,
      SendTTSMessages: 4096n,
      SendVoiceMessages: 70368744177664n,
      SendPolls: 562949953421312n,
      Connect: 1048576n,
      Speak: 2097152n,
      Stream: 512n,
      UseSoundboard: 4398046511104n,
      UseExternalSounds: 35184372088832n,
      UseVAD: 33554432n,
      PrioritySpeaker: 256n,
      MuteMembers: 4194304n,
      DeafenMembers: 8388608n,
      MoveMembers: 16777216n,
      SetVoiceChannelStatus: 281474976710656n,
      UseApplicationCommands: 2147483648n,
      UseEmbeddedActivities: 549755813888n,
      UseExternalApps: 1125899906842624n,
      RequestToSpeak: 4294967296n,
      CreateEvents: 17592186044416n,
      ManageEvents: 8589934592n
    })
  );
  const DiscordGuildPermissions = Object.freeze(
    ignoreProtoField({
      __proto__: null,
      ViewChannel: 1024n,
      ManageChannels: 16n,
      ManageRoles: 268435456n,
      CreateGuildExpressions: 8796093022208n,
      ManageGuildExpressions: 1073741824n,
      ViewAuditLog: 128n,
      ViewGuildInsights: 524288n,
      ManageWebhooks: 536870912n,
      ManageGuild: 32n,
      CreateInstantInvite: 1n,
      ChangeNickname: 67108864n,
      ManageNicknames: 134217728n,
      KickMembers: 2n,
      BanMembers: 4n,
      ModerateMembers: 1099511627776n,
      SendMessages: 2048n,
      SendMessagesInThreads: 274877906944n,
      CreatePublicThreads: 34359738368n,
      CreatePrivateThreads: 68719476736n,
      EmbedLinks: 16384n,
      AttachFiles: 32768n,
      AddReactions: 64n,
      UseExternalEmojis: 262144n,
      UseExternalStickers: 137438953472n,
      MentionEveryone: 131072n,
      ManageMessages: 8192n,
      ManageThreads: 17179869184n,
      ReadMessageHistory: 65536n,
      SendTTSMessages: 4096n,
      SendVoiceMessages: 70368744177664n,
      SendPolls: 562949953421312n,
      Connect: 1048576n,
      Speak: 2097152n,
      Stream: 512n,
      UseSoundboard: 4398046511104n,
      UseExternalSounds: 35184372088832n,
      UseVAD: 33554432n,
      PrioritySpeaker: 256n,
      MuteMembers: 4194304n,
      DeafenMembers: 8388608n,
      MoveMembers: 16777216n,
      SetVoiceChannelStatus: 281474976710656n,
      UseApplicationCommands: 2147483648n,
      UseEmbeddedActivities: 549755813888n,
      UseExternalApps: 1125899906842624n,
      RequestToSpeak: 4294967296n,
      CreateEvents: 17592186044416n,
      ManageEvents: 8589934592n,
      ViewCreatorMonetizationAnalytics: 2199023255552n,
      Administrator: 8n
      // Unknown?: 140737488355328n
    })
  );

  let rolesJsonStr = $state("");
  let channelsJsonStr = $state("");
  let roles: { name: string; id: bigint; perms: bigint }[] = $state([]);
  let channelPermissionOverwrites: { channelName: string; perms: { type: number; id: bigint; allow: bigint; deny: bigint }[] }[] = $state(
    []
  );

  type JsonValue = number | boolean | string | null | Partial<{ [prop: string]: JsonValue }> | JsonValue[];
  const rolesJsonStrOnInput = () => {
    try {
      const parsed: JsonValue = JSON.parse(rolesJsonStr);
      roles = (
        parsed as {
          id: string;
          name: string;
          permissions: string;
        }[]
      ).map(v => ({
        name: `${v.name}`,
        id: BigInt(v.id),
        perms: BigInt(v.permissions)
      }));
    } catch (e) {
      console.error(e);
    }
  };

  const channelsJsonStrOnInput = () => {
    try {
      const parsed: JsonValue = JSON.parse(channelsJsonStr);
      channelPermissionOverwrites = (
        parsed as {
          id: string;
          name: string;
          permission_overwrites?: {
            id: string;
            type: number;
            allow: string;
            deny: string;
          }[];
        }[]
      )
        .map(v => ({
          channelName: v.name,
          perms: (v.permission_overwrites ?? [])
            .map(v => ({
              id: BigInt(v.id),
              type: +v.type,
              allow: BigInt(v.allow),
              deny: BigInt(v.deny)
            }))
            .filter(v => v.allow || v.deny)
        }))
        .filter(v => v.perms.length);
    } catch (e) {
      console.error(e);
    }
  };

  rolesJsonStrOnInput();
  channelsJsonStrOnInput();
</script>

<main style="padding: 0.5em;">
  <div style="display: grid; place-content: center; place-items: center;">
    <div style="display: block;">
      <div>
        <span>Roles data(json):</span>
        <br />
        <textarea bind:value={rolesJsonStr} oninput={rolesJsonStrOnInput} style="max-width: 80svw; width: 50svw; height: 8em;"></textarea>
      </div>
      <div>
        <span>Channels data(json):</span>
        <br />
        <textarea bind:value={channelsJsonStr} oninput={channelsJsonStrOnInput} style="max-width: 80svw; width: 50svw; height: 8em;"></textarea>
      </div>
      <div style="padding: 0.6em;">
        <div style="font-size: x-large; font-weight: bold;">Guild default</div>
        {#each roles as role}
          <div style="padding: 0.6em;">
            <div>
              <span style="font-size: large; font-weight: bold;">Role: <span style="white-space: pre-wrap;">{role.name}</span></span>
              <span style="color: #AAA;">({role.id})</span>
            </div>
            <div style="padding: 0.6em;">
              {#each Object.entries(DiscordGuildPermissions) as discordPerm}
                <div>
                  {discordPerm[0]}: {#if discordPerm[1] & role.perms}
                    <span><img style="height: 1em;" src="./check.svg" alt="✅" /></span>
                  {:else}
                    <span><img style="height: 1em;" src="./cross.svg" alt="x" /></span>
                  {/if}
                </div>
              {/each}
            </div>
          </div>
        {/each}
      </div>
      {#each channelPermissionOverwrites as permissionOverwrites}
        <div style="padding: 0.6em;">
          <div style="font-size: x-large; font-weight: bold;"># <span style="white-space: pre-wrap;">{permissionOverwrites.channelName}</span></div>
          {#each permissionOverwrites.perms as perm}
            <div style="padding: 0.6em;">
              {#if perm.type === 0}
                <div>
                  <span style="font-size: large; font-weight: bold;">Role: <span style="white-space: pre-wrap;">{roles.find(r => r.id === perm.id)?.name ?? "Unknown role"}</span></span>
                  <span style="color: #AAA;">({perm.id})</span>
                </div>
              {:else if perm.type === 1}
                <div>
                  <span style="font-size: large; font-weight: bold;">User: {perm.id}</span>
                </div>
              {:else}
                <div>
                  <span style="font-size: large; font-weight: bold;">Unknown: {perm.id}</span>
                </div>
              {/if}
              <div style="padding: 0.6em;">
                {#each Object.entries(DiscordChannelPermissions) as discordPerm}
                  {#if discordPerm[1] & perm.allow}
                    <div>
                      {discordPerm[0]}: <span><img style="height: 1em;" src="./check.svg" alt="✅" /></span>
                    </div>
                  {:else if discordPerm[1] & perm.deny}
                    <div>
                      {discordPerm[0]}: <span><img style="height: 1em;" src="./cross.svg" alt="x" /></span>
                    </div>
                  {/if}
                {/each}
              </div>
            </div>
          {/each}
        </div>
      {/each}
    </div>
  </div>
</main>
