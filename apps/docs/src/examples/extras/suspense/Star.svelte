<script lang="ts">
  import { useFrame } from '@threlte/core'

  // your script goes here

  import { Instance } from '@threlte/extras'
  import { Color } from 'three'

  let positionX = Math.random() * 100 - 50
  let positionY = Math.random() * 100 - 50
  let positionZ = 100

  const colors = ['#FFF09E', '#B8DFFF', '#CADBFF', '#FFEBBE']

  // random element from array
  const color = new Color(colors[Math.floor(Math.random() * colors.length)])

  useFrame(() => {
    positionZ -= 10
  })
</script>

<Instance
  {color}
  position.x={positionX}
  position.y={positionY}
  position.z={positionZ}
/>
