<template>
  <div class="relative-position">
    <div class="row">
      <div class="col-12">
        <q-card
          class="q-mb-sm"
          v-for="(note, index) in table.data"
          :key="index">
          <q-card-main>
            <q-input
              type="textarea"
              v-model="note.body"
              v-if="note.edit"/>
            <p align="justify" v-else>
              {{note.body}}
            </p>
          </q-card-main>
          <q-card-actions class="row">
            <div
              class="col-12"
              v-if="note.edit">
              <q-btn
                dense
                flat
                icon="save"
                @click="updateItem(note)"
                class="full-width"/>
            </div>
            <div class="col-6">
              <q-btn
                :label="$tr('ui.label.cancel')"
                v-if="note.edit"
                @click="note.edit = false"/>
              <q-btn
                v-else
                dense
                flat
                icon="edit"
                class="full-width"
                @click="note.edit = true"/>
            </div>
            <div class="col-6">
              <q-btn
                dense
                flat
                icon="delete"
                @click="deleteItem(note)"
                class="full-width"/>
            </div>
          </q-card-actions>
        </q-card>
      </div>
    </div>
    <div class="row">
      <div class="col-12 q-mb-md">
        <q-input
          v-if="newNote.openForm"
          rows="3"
          type="textarea"
          v-model="newNote.body"/>
      </div>
      <div class="col-6 q-mb-sm">
        <q-btn
          @click="createtem()"
          class="full-width"
          :label="$tr('ui.label.create')"
          v-if="newNote.openForm"/>
      </div>
      <div class="col-6">
        <q-btn
          class="full-width"
          :label="$tr('ui.label.cancel')"
          v-if="newNote.openForm"
          @click="newNote.openForm = false"/>
      </div>
      <div class="col-12">
        <q-btn
          class="full-width"
          icon="add"
          color="primary"
          @click="newNote.openForm = true"/>
      </div>
    </div>
    <inner-loading :visible="loading"/>
  </div>
</template>

<script>
  export default {
    components: {
    },
    mounted() {
      this.$nextTick(function () {
        this.getDataTable(false)
      })
    },
    data() {
      return {
        loading: false,
        newNote:{
          userId: this.$store.state.quserAuth.userData.id,
          body:'',
          openForm: false,
        },
        table: {
          data: [],
          columns: [],
          pagination: {
            page: 1,
            rowsNumber: '',
            rowsPerPage: 10
          },
          filter: {
            search: null
          },
          filters: {
          },
          options: {
          }
        },
      }
    },
    methods: {
      // Request products with params from server table
      getDataTable(refresh = false) {
        this.getdata({pagination: this.table.pagination}, refresh)
      },
      // Get products
      getdata({pagination, filter}, refresh = false) {
        this.loading = true
        // arams to request
        let params = {
          refresh: refresh,
          params: {
            page: pagination.page,
            take: pagination.rowsPerPage,
            filter:{
              userId: this.$store.state.quserAuth.userData.id
            }
          }
        }
        //Request
        this.$crud.index('apiRoutes.qnote.notes', params).then(response => {
          this.table.data = response.data.map( item => {
            return {
              id: item.id,
              body: item.body,
              userId: item.userId,
              edit: false,
            }
          })
          this.table.pagination.page = response.meta.page.currentPage
          this.table.pagination.rowsNumber = response.meta.page.total
          this.table.pagination.rowsPerPage = pagination.rowsPerPage
          this.loading = false
        }).catch(error => {
          this.loading = false
          this.$alert.error({message: this.$tr('ui.message.errorRequest'), pos: 'bottom'})
        })
      },
      // Create
      createtem() {
        this.loading = true
        let configName = 'apiRoutes.qnote.notes'
        this.$crud.create(configName, this.newNote)
        .then( response => {
          this.newNote.body = ''
          this.$helper.alert.success({message: this.$tr('ui.message.recordCreated'), pos: 'bottom'})
          this.getDataTable(true)
          this.newNote.openForm = false
        })
        .catch( error => {
          this.$alert.error({message: this.$tr('ui.message.errorRequest'), pos: 'bottom'})
          this.loading = false
        })
      },
      // Update
      updateItem(item){
        this.loading = true
        let configName = 'apiRoutes.qnote.notes'
        this.$crud.update(configName, item.id, item, {params: {}})
        .then(response => {
          item.edit = false
          this.$helper.alert.success({message: this.$tr('ui.message.recordUpdated'), pos: 'bottom'})
          this.getDataTable(true)
          this.loading = false
        }).catch(error => {
          this.$alert.error({message: this.$tr('ui.message.errorRequest'), pos: 'bottom'})
          this.loading = false
        })
      },
      // Delete
      deleteItem(item) {
        this.$q.dialog({
          title: ' ',
          message: this.$tr('ui.message.deleteRecord'),
          color: 'negative',
          ok: 'Delete',
          cancel: true
        }).then(data => {
          this.loading = true
          let configName = 'apiRoutes.qnote.notes'
          this.$crud.delete(configName, item.id, {params: {}}).then(response => {
            this.$helper.alert.success({message: this.$tr('ui.message.recordDeleted'), pos: 'bottom'})
            this.getDataTable(true)
            this.loading = false
          }).catch(error => {
            this.$alert.error({message: this.$tr('ui.message.errorRequest'), pos: 'bottom'})
            this.loading = false
          })
        }).catch(data => {})
      },
    }
  }
</script>

<style scoped>

</style>
