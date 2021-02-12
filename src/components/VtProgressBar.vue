<template>
  <div :style="style" :class="cpClass" />
</template>

<script lang="ts">
import { defineComponent } from "vue"

import { VT_NAMESPACE } from "../ts/constants"
import PROPS from "../ts/propValidators"

export default defineComponent({
  name: "VtProgressBar",

  props: PROPS.PROGRESS_BAR,

  // TODO: The typescript compiler is not playing nice with emit types
  // Rollback this change once ts is able to infer emit types
  emits: ["close-toast"],

  data() {
    return {
      nsExtension: "",
      hasClass: true,
      // eslint-disable-next-line @typescript-eslint/no-explicit-any
      timeoutRef: null as any,
    }
  },

  computed: {
    style(): {
      animationDuration: string
      animationPlayState: string
      opacity: number
    } {
      return {
        animationDuration: `${this.timeout}ms`,
        animationPlayState: this.isRunning ? "running" : "paused",
        opacity: this.hideProgressBar ? 0 : 1,
      }
    },

    cpClass(): string {
      return this.hasClass
        ? `${VT_NAMESPACE}${this.nsExtension}__progress-bar`
        : ""
    },
  },

  watch: {
    timeout() {
      this.hasClass = false
      // eslint-disable-next-line @typescript-eslint/ban-ts-comment
      // @ts-ignore
      this.$nextTick(() => (this.hasClass = true))
    },
  },

  mounted() {
    if (this.timeout)
      this.timeoutRef = setTimeout(() => {
        this.$emit("close-toast")
      }, this.timeout)
  },

  beforeUnmount() {
    if (this.timeout && this.timeoutRef) clearTimeout(this.timeoutRef)
  },
})
</script>
