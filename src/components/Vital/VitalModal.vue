<template>
  <b-modal :id="$attrs.id" size="xl" hide-footer no-close-on-backdrop centered>
    <template #modal-header="{ close }">
      <h5 class="modal-title"><slot></slot></h5>
      <button
        type="button"
        class="close"
        data-dismiss="modal"
        aria-label="Close"
        @click="
          resetData()
          close()
        "
      >
        <span aria-hidden="true">&times;</span>
      </button>
    </template>
    <slot name="input"> </slot>
  </b-modal>
</template>
<script>
export default {
  name: 'VitalModal',
  methods: {
    resetData() {
      this.$store.dispatch('vital/resetData')
    },
  },
}
</script>
