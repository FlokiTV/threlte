<script lang="ts">
  import { onMount } from 'svelte'
  import { c } from '../../lib/classes'
  import OpenInStackblitz from './OpenInStackblitz.svelte'

  export let path: string
  export let files: Record<string, string>
  export let hideCode: boolean
  export let iframe: boolean

  const allAppModules = import.meta.glob('../../examples/**/App.svelte') as Record<
    string,
    () => Promise<any>
  >

  let mounted = false

  const AppModule = Object.entries(allAppModules).find(
    ([key]) => key.includes(path) && key.endsWith('App.svelte')
  )?.[1]

  onMount(() => {
    mounted = true
  })

  let _class = ''
  export { _class as class }
</script>

<div
  class={c(
    'relative h-[80vh] w-full overflow-hidden rounded-t-md border border-white/20 bg-blue-900',
    hideCode && '!rounded-md',
    _class
  )}
>
  {#if iframe}
    <iframe
      src="/examples/{path}"
      title={path}
      class="w-full h-full border-none"
    />
  {:else if mounted && AppModule}
    {#await AppModule() then Mod}
      <Mod.default />
    {/await}
  {/if}

  <div class="absolute right-4 bottom-4">
    <OpenInStackblitz {files} />
  </div>
</div>
