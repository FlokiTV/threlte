<script lang="ts">
  import CopyCodeButton from '../Code/CopyCodeButton.svelte'
  import InstallButton from './InstallButton.svelte'
  import { onMount } from 'svelte'

  let useGltf = false
  let installExtras = false
  let installRapier = false
  let installTheatre = false
  let installXR = false
  let installTypes = false

  let divider = ' \\'
  let merger = '\n'
  let space = '            '

  $: installCode = [
    `npm install three @threlte/core${
      installExtras || useGltf || installRapier || installTheatre || installXR || installTypes ? divider : ''
    }`,
    (installExtras || useGltf || installTheatre) &&
      `${space}@threlte/extras${installRapier || installTheatre || installXR || installTypes ? divider : ''}`,
    installRapier &&
      `${space}@threlte/rapier @dimforge/rapier3d-compat${
        installTypes || installTheatre || installXR ? divider : ''
      }`,
    installTheatre &&
      `${space}@threlte/theatre @theatre/core @theatre/studio${installXR || installTypes ? divider : ''}`,
    installXR && `${space}@threlte/xr${installTypes ? divider : ''}`,
    installTypes && `${space}@types/three`
  ]
    .filter(Boolean)
    .join(merger)

  onMount(() => {
    if (window.navigator.userAgent.includes('Windows')) {
      divider = ' '
      merger = ''
      space = ''
    }
  })
</script>

<div
  class="grid grid-cols-1 items-start justify-start gap-x-4 gap-y-2 max-md:justify-items-start md:grid-cols-[auto_auto] md:gap-y-2 md:[&>button]:my-1"
>
  <InstallButton
    disabled
    active
    class="cursor-not-allowed">@threlte/core</InstallButton
  >

  <p class="my-0 self-center text-sm md:text-base">
    Compose three.js scenes declaratively and state-driven. Three.js and Threlte's core the library
    are required.
  </p>

  <hr class="m-0 w-full p-0 opacity-50 max-md:my-3 md:col-span-2" />

  <InstallButton
    on:click={() => {
      installExtras = !installExtras
    }}
    active={installExtras}
    passivelyActive={useGltf || installTheatre}>@threlte/extras</InstallButton
  >

  <p class="my-0 self-center text-sm md:text-base">
    <a
      href="/docs/reference/extras/getting-started"
      target="_blank">Components, helpers, hooks</a
    >
    and more that extend the core functionality.
  </p>

  <hr class="m-0 w-full p-0 opacity-50 max-md:my-3 md:col-span-2" />

  <InstallButton
    on:click={() => {
      useGltf = !useGltf
    }}
    active={useGltf}>@threlte/gltf</InstallButton
  >

  <p class="my-0 self-center text-sm md:text-base">
    A <a
      href="/docs/reference/gltf/getting-started"
      target="_blank">command-line tool</a
    >
    that turns GLTF assets into declarative and re-usable Threlte components. The generated Threlte components
    make use of the package <code>@threlte/extras</code>.
  </p>

  <hr class="m-0 w-full p-0 opacity-50 max-md:my-3 md:col-span-2" />

  <InstallButton
    on:click={() => {
      installRapier = !installRapier
    }}
    active={installRapier}>@threlte/rapier</InstallButton
  >

  <p class="my-0 self-center text-sm md:text-base">
    Components and hooks to use the <a
      href="https://rapier.rs/"
      target="_blank"
      rel="noreferrer">Rapier physics engine</a
    > in Threlte.
  </p>

  <hr class="m-0 w-full p-0 opacity-50 max-md:my-3 md:col-span-2" />

  <InstallButton
    on:click={() => {
      installTheatre = !installTheatre
    }}
    active={installTheatre}>@threlte/theatre</InstallButton
  >

  <p class="my-0 self-center text-sm md:text-base">
    Components and hooks to use the animation library <a
      href="https://www.theatrejs.com/"
      target="_blank"
      rel="noreferrer">Theatre.js</a
    > in Threlte.
  </p>

  <hr class="m-0 w-full p-0 opacity-50 max-md:my-3 md:col-span-2" />

  <InstallButton
    on:click={() => {
      installXR = !installXR
    }}
    active={installXR}>@threlte/xr</InstallButton
  >

  <p class="my-0 text-sm md:text-base self-center">
    Components and hooks for VR and AR.
  </p>

  <hr class="md:col-span-2 p-0 m-0 opacity-50 w-full max-md:my-3" />


  <InstallButton
    on:click={() => {
      installTypes = !installTypes
    }}
    active={installTypes}>@types/three</InstallButton
  >

  <p class="my-0 self-center text-sm md:text-base">TypeScript types for three.js.</p>
</div>

<p>Install the packages with npm, pnpm, yarn or any other package manager you prefer.</p>

<div
  class="not-prose group relative overflow-x-auto whitespace-pre-wrap rounded-md border border-white/20 bg-blue-900 p-3 text-sm shadow-xl [&>*]:!bg-transparent"
>
  <code class="p-0 text-[1em]">
    {installCode}
  </code>

  <CopyCodeButton code={installCode} />
</div>
