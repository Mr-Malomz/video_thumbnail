<template>
  <div class="flex flex-col items-center p-8">
    <h1 class="text-xl font-medium text-blue-900 mb-8">Cloudinary Video Preview</h1>
    <div class="grid grid-cols-1 lg:grid-cols-3 gap-3">
      <div
        class
        v-for="(video, i) in videoList"
        :key="i"
        @mouseenter="onHoverEnter(i)"
        @mouseleave="onHoverLeave(i)"
      >
        <video class="w-96 h-96" :id="`video-player${video.id}`" muted :controls="hovered"></video>
      </div>
    </div>
  </div>
</template>

<script>
import videoList from '@/utils/videoList.json'

export default {
  name: 'IndexPage',
  data() {
    return {
      cld: null,
      player: null,
      hovered: false,
      videoList,
    }
  },

  mounted() {
    this.player = this.videoList;

    this.cld = cloudinary.Cloudinary.new({
      cloud_name: process.env.NUXT_ENV_CLOUDINARY_CLOUD_NAME,
      secure: true,
    })

    this.videoList.forEach((video, i) => {
      this.player[i] = this.cld.videoPlayer(`video-player${video.id}`, {
        transformation: { effect: "preview:duration_5" }
      })

      this.player[i].source(`${video.publicId}.jpg`)
    });
  },

  methods: {
    onHoverEnter(id) {
      this.player[id].source(this.videoList[id].publicId)
      this.player[id].play()
      this.hovered = true
    },

    onHoverLeave(id) {
      this.player[id].source(this.videoList[id].publicId)
      this.player[id].stop()
      this.hovered = false
    },
  },
}
</script>
