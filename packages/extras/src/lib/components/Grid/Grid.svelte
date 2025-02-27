<!-- Credits to Fyrestar for the https://github.com/Fyrestar/THREE.InfiniteGridHelper  -->
<script lang="ts">
  import type { GridProps, GridEvents, GridSlots } from './Grid.svelte'
  import { useThrelte, T, forwardEventHandlers } from '@threlte/core'
  import { Color, ShaderMaterial, DoubleSide, type Mesh } from 'three'
  import { revision } from '../../lib/revision'

  type $$Props = Required<GridProps>
  type $$Events = GridEvents
  type $$Slots = GridSlots

  export let cellColor: $$Props['cellColor'] = '#000000'
  export let sectionColor: $$Props['sectionColor'] = '#0000ee'
  export let cellSize: $$Props['cellSize'] = 1
  export let sectionSize: $$Props['sectionSize'] = 10
  export let axes: $$Props['axes'] = 'xzy'
  export let gridSize: $$Props['gridSize'] = [20, 20]
  export let followCamera: $$Props['followCamera'] = false
  export let infiniteGrid: $$Props['infiniteGrid'] = false
  export let fadeDistance: $$Props['fadeDistance'] = 100
  export let fadeStrength: $$Props['fadeStrength'] = 1
  export let cellThickness: $$Props['cellThickness'] = 1
  export let sectionThickness: $$Props['sectionThickness'] = 2

  // forward ref binding
  export let ref: Mesh

  const { invalidate } = useThrelte()

  const makeGridMaterial = (axes: $$Props['axes']) => {
    return new ShaderMaterial({
      side: DoubleSide,

      uniforms: {
        uSize1: {
          value: cellSize
        },
        uSize2: {
          value: sectionSize
        },
        uColor1: {
          value: new Color(cellColor)
        },
        uColor2: {
          value: new Color(sectionColor)
        },
        uFadeDistance: {
          value: fadeDistance
        },
        uFadeStrength: {
          value: fadeStrength
        },
        uThickness1: {
          value: 1
        },
        uThickness2: {
          value: 1
        },
        uInfiniteGrid: {
          value: infiniteGrid ? 1 : 0
        },
        uFollowCamera: {
          value: 0
        }
      },
      transparent: true,
      vertexShader: `
      varying vec3 worldPosition;
      uniform float uFadeDistance;
      uniform float uInfiniteGrid;
      uniform float uFollowCamera;

      void main() {

        vec3 pos = position.${axes} * (1. + uFadeDistance * uInfiniteGrid);
        pos.${axes.slice(0, 2)} += (cameraPosition.${axes.slice(0, 2)} * uFollowCamera);

        worldPosition = pos;

        gl_Position = projectionMatrix * modelViewMatrix * vec4(pos, 1.0);

      }`,

      fragmentShader: `
      varying vec3 worldPosition;
      uniform float uSize1;
      uniform float uSize2;
      uniform vec3 uColor1;
      uniform vec3 uColor2;
      uniform float uFadeDistance;
      uniform float uFadeStrength;
      uniform float uThickness1;
      uniform float uThickness2;
      uniform float uInfiniteGrid;

      float getGrid(float size, float thickness) {

        vec2 r = worldPosition.${axes.slice(0, 2)} / size;

        vec2 grid = abs(fract(r - 0.5) - 0.5) / fwidth(r);
        float line = min(grid.x, grid.y) + 1. - thickness;

        return 1.0 - min(line, 1.);
      }

      void main() {

        float g1 = getGrid(uSize1, uThickness1);
        float g2 = getGrid(uSize2, uThickness2);

        float d = 1.0 - min(distance(cameraPosition.${axes.slice(0, 2)}, worldPosition.${axes.slice(
        0,
        2
      )}) / uFadeDistance, 1.);
        vec3 color = mix(uColor1, uColor2, min(1.,uThickness2*g2));

        gl_FragColor = vec4(color, (g1 + g2) * pow(d,uFadeStrength));
        gl_FragColor.a = mix(0.75 * gl_FragColor.a, gl_FragColor.a, g2);

        if(gl_FragColor.a <= 0.0)
          discard;
        #include <tonemapping_fragment>
        #include <${revision < 154 ? 'encodings_fragment' : 'colorspace_fragment'}>
      }
       `,

      extensions: {
        derivatives: true
      }
    })
  }

  $: material = makeGridMaterial(axes)
  $: material && invalidate('Grid axes changed')

  $: {
    axes
    material.uniforms.uSize1 = { value: cellSize }
    material.uniforms.uSize2 = { value: sectionSize }
    material.uniforms.uColor1 = { value: new Color(cellColor) }
    material.uniforms.uColor2 = { value: new Color(sectionColor) }
    material.uniforms.uFadeDistance = { value: fadeDistance }
    material.uniforms.uFadeStrength = { value: fadeStrength }
    material.uniforms.uThickness1 = { value: cellThickness }
    material.uniforms.uThickness2 = { value: sectionThickness }
    material.uniforms.uFollowCamera = { value: followCamera ? 1 : 0 }
    material.uniforms.uInfiniteGrid = { value: infiniteGrid ? 1 : 0 }
    invalidate('Grid uniforms changed')
  }

  const component = forwardEventHandlers()
</script>

<T.Mesh
  bind:this={$component}
  bind:ref
  {material}
  frustumCulled={false}
  {...$$restProps}
  let:ref
>
  <T.PlaneGeometry args={typeof gridSize == 'number' ? [gridSize, gridSize] : gridSize} />

  <slot {ref} />
</T.Mesh>
