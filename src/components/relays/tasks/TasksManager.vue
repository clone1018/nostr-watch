<template>
  <!-- <StatusCheckAPI /> -->
  <GetRelays />
  <DetectRegion 
    v-if="store.prefs.autoDetectRegion" />
  <StatusCheckHistoryNode />
  <HeartbeatTask />
  <LoadSeed 
    v-bind:resultsProp="results"
    v-if="!store.prefs.clientSideProcessing || isSingle" />
  <CheckNip11
    v-bind:resultsProp="results"
    v-if="(!store.prefs.clientSideProcessing || ( !store.prefs.clientSideProcessing && isSingle ) ) && store.prefs.checkNip11" />
  <RefreshTask
    v-bind:resultsProp="results"
    v-if="store.prefs.clientSideProcessing || isSingle" />
  <GetTopics
    v-bind:resultsProp="results"
    v-if="store.prefs.clientSideProcessing && !isSingle" />
  <UserRelayList />
  <!-- <RelayCanonicalsTask
    :resultsProp="results" />
  <RelayOperatorTask
    :resultsProp="results" /> -->
</template>

<script>
import { defineComponent, toRefs } from 'vue'

import { setupStore } from '@/store'

import SharedComputed from '@/shared/computed.js'

import DetectRegion from './DetectRegion.vue'
import LoadSeed from './LoadSeed.vue'
import RefreshTask from './RefreshTask.vue'
import HeartbeatTask from './HeartbeatTask.vue'
import UserRelayList from './UserRelayList.vue'
import StatusCheckHistoryNode from './StatusCheckHistoryNode.vue'
// import StatusCheckAPI from './StatusCheckAPI.vue'
import GetRelays from './GetRelays.vue'
import CheckNip11 from './CheckNip11.vue'

// import RelayCanonicalsTask from './RelayCanonicalsTask.vue'
// import RelayOperatorTask from './RelayOperatorTask.vue'

export default defineComponent({
  name: "TasksManager",
  components: {
    DetectRegion,
    LoadSeed,
    RefreshTask,
    HeartbeatTask,
    UserRelayList,
    StatusCheckHistoryNode,
    // StatusCheckAPI,
    GetRelays,
    CheckNip11
    // RelayCanonicalsTask,
    // RelayOperatorTask
  },
  data(){
    return {
      interval: null,
      currentTask: null
    }
  },
  setup(props){
    const {resultsProp: results} = toRefs(props)
    return { 
      store : setupStore(),
      results: results
    }
  },
  beforeMount(){},
  mounted(){
    setTimeout( () => {
      this.currentTask = this.store.tasks.currentTask
      this.processJob()
      
      this.interval = setInterval( () => {
        if(this.currentTask === this.store.tasks.currentTask)
          return 
        this.processJob()
        this.currentTask = this.store.tasks.currentTask
      }, 1000)
    }, 500)
    setTimeout( ()=>{}, 1)
  },
  unmounted(){
    clearInterval(this.interval)
  },
  props: {
    resultsProp: {
      type: Object,
      default(){
        return {}
      }
    },
  },
  methods: {
    processJob(){
      if(!this.store.tasks.active?.handler)
        return 
      this.store.tasks.active.handler()
    }
  },
  computed: Object.assign(SharedComputed, {})
});
</script>