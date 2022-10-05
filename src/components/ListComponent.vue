<template>
    <div class="row items-center" >
      <!-- <div > -->
        <div class="q-pa-md">
          <q-table
            title="Today: My Tasks"
            :rows="rows"
            :columns="columns"
            row-key="name"
            :selected-rows-label="getSelectedString"
            selection="multiple"
            v-model:selected="selected"
          />
        </div>
      <!-- </div> -->
      <!-- <div >
        <div class="q-pa-md">
          <q-table
            title="Doing"
            :rows="rows"
            :columns="columns"
            row-key="name"
            :selected-rows-label="getSelectedString"
            selection="multiple"
            v-model:selected="selected"
          />
        </div>
      </div>
      <div>
        <div class="q-pa-md">
          <q-table
            title="Done"
            :rows="rows"
            :columns="columns"
            row-key="name"
            :selected-rows-label="getSelectedString"
            selection="multiple"
            v-model:selected="selected"
          />
        </div>
      </div> -->
    </div>
  
  
    <q-dialog v-model="prompt" persistent>
      <q-card style="min-width: 360px">
        <q-card-section>
          <div class="text-h6 text-center">Input your mission</div>
        </q-card-section>

        <q-card-section class="q-pt-none">
          <q-input rounded outlined v-model="address" autofocus @keyup.enter="prompt=true" placeholder="Task Name" />
        </q-card-section>

        <q-card-section class="q-pt-none">
          <q-input rounded outlined label="Note"
            v-model="address"
            type="textarea"
          />
        </q-card-section>

        <q-card-section class="q-pt-none">
          <q-select rounded outlined v-model="model" :options="options" label="Priority" />
        </q-card-section>

        <q-card-section class="q-pt-none">
          <q-input filled v-model="date" label="Start date">
            <template v-slot:prepend>
              <q-icon name="event" class="cursor-pointer">
                <q-popup-proxy cover transition-show="scale" transition-hide="scale">
                  <q-date v-model="date" mask="YYYY-MM-DD HH:mm">
                    <div class="row items-center justify-end">
                      <q-btn v-close-popup label="Close" color="primary" flat />
                    </div>
                  </q-date>
                </q-popup-proxy>
              </q-icon>
            </template>

            <template v-slot:append>
              <q-icon name="access_time" class="cursor-pointer">
                <q-popup-proxy cover transition-show="scale" transition-hide="scale">
                  <q-time v-model="date" mask="YYYY-MM-DD HH:mm" format24h>
                    <div class="row items-center justify-end">
                      <q-btn v-close-popup label="Close" color="primary" flat />
                    </div>
                  </q-time>
                </q-popup-proxy>
              </q-icon>
            </template>
          </q-input>
          
        </q-card-section>

        <q-card-section class="q-pt-none">
          <q-input filled v-model="date" label="Due date">
            <template v-slot:prepend>
              <q-icon name="event" class="cursor-pointer">
                <q-popup-proxy cover transition-show="scale" transition-hide="scale">
                  <q-date v-model="date" mask="YYYY-MM-DD HH:mm">
                    <div class="row items-center justify-end">
                      <q-btn v-close-popup label="Close" color="primary" flat />
                    </div>
                  </q-date>
                </q-popup-proxy>
              </q-icon>
            </template>

            <template v-slot:append>
              <q-icon name="access_time" class="cursor-pointer">
                <q-popup-proxy cover transition-show="scale" transition-hide="scale">
                  <q-time v-model="date" mask="YYYY-MM-DD HH:mm" format24h>
                    <div class="row items-center justify-end">
                      <q-btn v-close-popup label="Close" color="primary" flat />
                    </div>
                  </q-time>
                </q-popup-proxy>
              </q-icon>
            </template>
          </q-input>
          
        </q-card-section>
        

        <q-card-actions align="right" class="text-primary">
          <q-btn unelevated rounded color="accent" label="Cancel" v-close-popup />
          <q-btn unelevated rounded color="primary" label="Add Tasks" v-close-popup />
          <q-btn unelevated rounded color="primary" label="Add Tasks and New" v-close-popup />
        </q-card-actions>
      </q-card>
    </q-dialog>

    
        <q-page-sticky position="bottom-right" :offset="[18, 18]">
            <!-- <q-btn fab icon="add" color="primary" @click="prompt=true" /> -->
            <q-btn fab icon="add" color="primary" @click="prompt=true" />
          </q-page-sticky>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue'
import { api } from 'boot/axios'
import { useQuasar } from 'quasar'

const columns = [
  {
    name: 'Time',
    required: true,
    label: 'Time',
    align: 'left',
    field: row => row.name,
    format: val => `${val}`,
    sortable: true
  },
  { name: 'Task', align: 'center', label: 'Task', field: 'Task', sortable: true },
  { name: 'Prority', label: 'Prority', field: 'Prority', sortable: true },
  { name: 'Status', label: 'Status', field: 'Status' },
]


export default defineComponent({
  name: 'ListComponent',
  props: {
    address: {
      type: String,
      required: false
    },
    alert: {
      type: Boolean
    },
    confirm: {
      type: Boolean
    },
    prompt: {
      type: Boolean
    },
  },
  methods: {

  },
  mounted: function() {
    this.loadData()
  },
  setup (props) {
    const selected = ref([])

    const $q = useQuasar()
    const rows = ref([])

    function loadData () {
      api.get('/rows')
        .then((response) => {
          rows.value = response.data
          console.log(rows)
        })
        .catch(() => {
          $q.notify({
            color: 'negative',
            position: 'top',
            message: 'Loading the data from API failed. Please check the connection.',
            icon: 'report_problem'
          })
        })
    }

    return {
            prompt: ref(false),
            address: ref(""),
            model: ref(null),
            options: [
              'High', 'Medium', 'Low'
            ],
            selected,
            columns,
            rows,

            getSelectedString () {
              return selected.value.length === 0 ? '' : `${selected.value.length} record${selected.value.length > 1 ? 's' : ''} selected of ${rows.length}`
            },
            date: ref('2019-02-01 12:44'),
            loadData,
          }
  }
  
})
</script>
