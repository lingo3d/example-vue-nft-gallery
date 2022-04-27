<script setup lang="ts">
import { World, Model, Find, HTML, ThirdPersonCamera, useSpring, types, Keyboard } from "lingo3d-vue"
import { computed, ref } from "vue"

const mouseOver = ref(false)
const bot = ref<types.Model>()
const pose = ref("idle")

const camX = computed(() => mouseOver.value ? 25 : 0)
const camY = computed(() => mouseOver.value ? 50 : 50)
const camZ = computed(() => mouseOver.value ? 50 : 200)

const xSpring = useSpring({ to: camX, bounce: 0 })
const ySpring = useSpring({ to: camY, bounce: 0 })
const zSpring = useSpring({ to: camZ, bounce: 0 })

const handleKeyPress = (key: string) => {
  if (key === "w") {
    pose.value = "running"
    bot.value?.moveForward(-10)
  }
}

const handleKeyUp = (key: string) => {
  if (key === "w")
    pose.value = "idle"
}

const outline = ref(false)

</script>

<template>
  <World
    default-light="env.hdr"
    skybox="env.hdr"
    ambientOcclusion
    bloom
    :bloom-strength="0.3"
    :bloom-radius="1"
    :bloomThreshold="0.8"
    outline-hidden-color="red"
    :outline-pulse="1000"
    outline-pattern="pattern.jpeg"
    :repulsion="1"
  >
    <Model src="gallery.glb" :scale="20" physics="map">
      <Find
        name="a6_CRN.a6_0"
        :outline="mouseOver"
        @mouse-over="mouseOver = true"
        @mouse-out="mouseOver = false"
      >
        <HTML v-if="mouseOver">
          <div style="color: white">
            <div style="font-weight: bold; font-size: 20" :duration="1000">
              Artwork Title
            </div>
            <div>
              Bird Watch
            </div>
          </div>
        </HTML>
      </Find>
    </Model>
    <ThirdPersonCamera
      mouse-control
      active
      :innerY="ySpring"
      :innerZ="zSpring"
      :innerX="xSpring"
    >
      <Model
        src="bot.fbx"
        ref="bot"
        physics="character"
        :animations='{ idle: "idle.fbx", running: "running.fbx" }'
        :animation="pose"
        :x="243.19"
        :y="-910.47"
        :z="-577.26"
        :outline="outline"
        pbr
      />
    </ThirdPersonCamera>
    <Keyboard @key-press="handleKeyPress" @key-up="handleKeyUp" />
  </World>
  <div style="position: absolute">
    <button @click="outline = !outline">toggle outline</button>
  </div>
</template>