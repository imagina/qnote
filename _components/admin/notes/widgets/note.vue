<template>
  <div>
    <q-btn
      @click="showMyNotes = !showMyNotes"
      no-caps
      color="primary"
      class="text-white"
      dense
      flat
      :label="$tr('qnote.layout.myNotes')"/>
    <div v-if="showMyNotes" class="fixed">
      <q-card
        inline
        color="white"
        class="text-black relative-position"
        style="min-width: 295px;position: relative;left: -100px;top: 14px; min-height: 105px">
        <q-card-main>
          <noteIndex/>
        </q-card-main>
      </q-card>
    </div>
  </div>
</template>

<script>
  import noteIndex from '@imagina/qnote/_components/admin/notes/index'
  export default {
    components:{
      noteIndex
    },
    data () {
      return {
        showMyNotes: false,
      }
    }
  }
</script>

<style scoped>

</style>
