<template>
    <div class="row" >
      <!-- <div > -->
        <div class="q-pa-md">
          <q-table
            title="Today: My Tasks"
            :rows="rows"
            :columns="columns"
            row-key="id"
            selection="multiple"
            v-model:selected="selected"
          >
          <template v-slot:top>
            <q-btn color="white" text-color="black" :disable="selected.length != 1" label="Edit" @click="prompt=true, currentTask = selected[0], isAddTask=false" icon-right="edit"/>
            <q-btn class="q-ml-sm" color="green" :disable="selected.length===0" label="Change Status" @click="updateStatus=true, currentStatus=null" icon-right="edit"/>
            <q-btn class="q-ml-sm" color="red" :disable="selected.length===0" label="Delete" @click="confirm=true" icon-right="delete"/>
          </template>
          <!-- <template v-slot:body="props">
            <q-tr :props="props" :class="(props.row.status=='Done')?'bg-accent text-white':'bg-white text-black'">
              <q-td key="time" :props="props">
                {{ props.row.time }}
              </q-td>
            </q-tr>
          </template> -->
        </q-table>
          
        </div>
      <!-- </div> -->
      <!-- <div class="q-mt-md">
        Selected: {{ JSON.stringify(selected) }}
      </div> -->
    </div>
  
  
    <q-dialog v-model="prompt" persistent>
      <q-card style="min-width: 360px">
        <q-card-section>
          <div class="text-h6 text-center">Input your mission</div>
        </q-card-section>

        <q-card-section class="q-pt-none">
          <q-input rounded outlined v-model="currentTask.task" 
          autofocus @keyup.enter="prompt=true" placeholder="Task Name" 
          lazy-rules
          :rules="[ val => val && val.length > 0 || 'Please input task name']"
          />
        </q-card-section>

        <q-card-section class="q-pt-none">
          <q-input rounded outlined label="Note"
            v-model="currentTask.note"
            type="textarea"
            lazy-rules
            :rules="[ val => val && val.length > 0 || 'Note some thing for this task.']"
          />
        </q-card-section>

        <q-card-section class="q-pt-none">
          <q-select rounded outlined v-model="currentTask.priority" :options="options" label="Priority"
            lazy-rules
            :rules="[ val => val && val.length > 0 || 'Please select the Priority for this task']"
           />
        </q-card-section>

        <q-card-section class="q-pt-none">
          <q-input filled v-model="currentTask.start" label="Start date"
          lazy-rules
            :rules="[ val => val && val.length > 0 || 'Please choose date']"
          >
            <template v-slot:prepend>
              <q-icon name="event" class="cursor-pointer">
                <q-popup-proxy cover transition-show="scale" transition-hide="scale">
                  <q-date v-model="currentTask.start" mask="YYYY-MM-DD HH:mm">
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
                  <q-time v-model="currentTask.start" mask="YYYY-MM-DD HH:mm" format24h>
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
          <q-input filled v-model="currentTask.end" label="Due date"
          lazy-rules
            :rules="[ val => val && val.length > 0 || 'Please choose date']"
          >
            <template v-slot:prepend>
              <q-icon name="event" class="cursor-pointer">
                <q-popup-proxy cover transition-show="scale" transition-hide="scale">
                  <q-date v-model="currentTask.end" mask="YYYY-MM-DD HH:mm">
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
                  <q-time v-model="currentTask.end" mask="YYYY-MM-DD HH:mm" format24h>
                    <div class="row items-center justify-end">
                      <q-btn v-close-popup label="Close" color="primary" flat />
                    </div>
                  </q-time>
                </q-popup-proxy>
              </q-icon>
            </template>
          </q-input>
          
        </q-card-section>
        

        <q-card-actions align="right" class="text-primary" v-if="isAddTask">
          <q-btn unelevated rounded color="accent" label="Cancel" v-close-popup />
          <q-btn unelevated rounded color="primary" label="Add Tasks" @click="createTask(currentTask, true)"/>
          <q-btn unelevated rounded color="primary" label="Add Tasks and New" @click="createTask(currentTask, false), currentTask={}" />
        </q-card-actions>
        <q-card-actions align="right" class="text-primary" v-if="!isAddTask">
          <q-btn unelevated rounded color="accent" label="Cancel" v-close-popup/>
          <q-btn unelevated rounded color="primary" label="Update" v-close-popup @click="updateTask(currentTask)" />
        </q-card-actions>
      </q-card>
    </q-dialog>

    <q-dialog v-model="confirm" persistent>
      <q-card>
        <q-card-section class="row items-center">
          <q-avatar icon="delete" color="primary" text-color="white" />
          <span class="q-ml-sm">Are you sure to delete {{selected.length}} tasks?</span>
        </q-card-section>

        <q-card-actions align="right">
          <q-btn flat label="Cancel" color="primary" v-close-popup />
          <q-btn flat label="Delete" color="primary" v-close-popup @click="deleteData(selected)" />
        </q-card-actions>
      </q-card>
    </q-dialog>

    <q-dialog v-model="updateStatus" persistent>
      <q-card style="min-width: 360px">
        <q-card-section>
          <div class="text-h6 text-center">Change Staus</div>
        </q-card-section>
        <q-card-section class="q-pt-none">
          <q-select rounded outlined v-model="currentStatus" :options="optionsStatus" label="Status" />
        </q-card-section>

        <q-card-actions align="right">
          <q-btn flat label="Cancel" color="primary" v-close-popup />
          <q-btn flat label="Change" color="primary" v-close-popup @click="changeStatus(currentStatus, selected)" :disable="currentStatus===null" />
        </q-card-actions>
      </q-card>
    </q-dialog>
    
  <q-page-sticky position="bottom-right" :offset="[18, 18]">
    <!-- <q-btn fab icon="add" color="primary" @click="prompt=true" /> -->
    <q-btn fab icon="add" color="primary" @click="prompt=true,initCreateTask(), currentTask={}, isAddTask=true" />
  </q-page-sticky>
</template>

<script lang="ts">
import { computed, defineComponent, ref, Ref } from 'vue'
import { api } from 'boot/axios'
import { useQuasar, date } from 'quasar'
import { Tasks } from './taksModels';
import { time } from 'console';

const columns = [
  {
    name: 'Time',
    required: true,
    label: 'Time',
    align: 'left',
    field: "time",
    sortable: true
  },
  { name: 'Task', align: 'center', label: 'Task', field: 'task', sortable: true },
  { name: 'Note', align: 'center', label: 'Note', field: 'note', sortable: true },
  { name: 'Prority', label: 'Prority', field: 'priority', sortable: true },
  { name: 'Status', label: 'Status', field: 'status' }
]


export default defineComponent({
  name: 'ListComponent',
  props: {
    
  },
  methods: {

  },
  mounted: function() {
    this.loadData()
  },
  setup (props) {
    var currentTask : Tasks = {}
    const prompt = ref(false)
    const selected  = ref([])
    const $q = useQuasar()
    const rows = ref([])

    const abc = ref({})

    function initCreateTask() {
      abc.value = {};
      const timeStamp = Date.now()
      currentTask.start = date.formatDate(timeStamp, 'YYYY-MM-DD HH:mm')
      currentTask.end = date.formatDate(timeStamp, 'YYYY-MM-DD HH:mm')
      console.log(currentTask)
    }

    function deleteData(tasks: Tasks[]) {
      // lists.forEach(element => {
      //   console.log(element)
      // })
      tasks.forEach(element => {
        api.delete('/tasks/' + element.id)
        .then(()=>{
            loadData()
          }
        )
        .catch(() => {
          $q.notify({
            color: 'negative',
            position: 'top',
            message: 'Loading the data from API failed. Please check the connection.',
            icon: 'report_problem'
          })
        })
      })
      
    }

    function createTask(task: Tasks, isClose: boolean) {
      if (!task.task || !task.note || !task.priority || !task.start || !task.end) {
        return
      }
      task.status = "New"
      task.time = task.start + " - " + task.end
      api.post('/tasks', task)
      .then((response)=>{
        loadData()
      })
      .catch((err)=>{
        console.log(err)
      })
      if (isClose) {
        prompt.value = false
      } else {
        prompt.value = true
      }
      
      return {prompt}
    }

    function updateTask(task:Tasks) {
      api.put('/tasks/' + task.id, task)
      .then(()=>{
        loadData()
      }).catch (()=>{
        $q.notify({
            color: 'negative',
            position: 'top',
            message: 'Loading the data from API failed. Please check the connection.',
            icon: 'report_problem'
          })
      })
    }

    function loadData (): void {
      api.get('/tasks')
        .then((response) => {
          rows.value = response.data
          selected.value  = []
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

    function showEdit() {
      currentTask = selected.value[0]
    }

    function changeStatus(currentStatus:any, tasks: Tasks[]) {
      tasks.forEach(element => {
        element.status = currentStatus
        api.put('/tasks/' + element.id , element)
        .then(()=>{
          // console.log("ok")
          loadData()
        }).catch(()=>{
          $q.notify({
            color: 'negative',
            position: 'top',
            message: 'Loading the data from API failed. Please check the connection.',
            icon: 'report_problem'
          })
        })
      })
      
      
    }

    return {
            isAddTask: ref(true),
            currentStatus: ref({}),
            updateStatus: ref(false),
            confirm: ref(false),
            prompt: prompt,
            address: ref(""),
            model: ref(null),
            options: [
              'High', 'Medium', 'Low'
            ],
            optionsStatus: [
              'New', 'Doing', 'Done'
            ],
            selected,
            columns,
            rows,

            getSelectedString () {
              return selected.value.length === 0 ? '' : `${selected.value.length} record${selected.value.length > 1 ? 's' : ''} selected of ${rows.value.length}`
            },
            date: ref('2019-02-01 12:44'),
            loadData,
            deleteData,
            currentTask: ref(null),
            showEdit,
            changeStatus,
            updateTask,
            createTask,
            initCreateTask
          }
  }
  
})
</script>
