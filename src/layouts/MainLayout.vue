<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-toolbar>

        <q-toolbar-title>
          IMWeb
        </q-toolbar-title>

        <div>Quasar v{{ $q.version }}</div>
      </q-toolbar>
    </q-header>

    <q-page-container>
      <q-virtual-scroll
    style="max-height: 300px;"
    :items="msgList"
    separator
  >
    <template v-slot="{ item, index }">
      <q-item
        :key="index"
        dense
      >
        <q-item-section>
          <q-item-label>
            #{{ index }} - {{ item.label }}<br/>
            #{{ item.time}}
          </q-item-label>
        </q-item-section>
      </q-item>
    </template>
  </q-virtual-scroll>
  <q-input filled bottom-slots v-model="text" label="Label" counter maxlength="12" :dense="dense">
        <template v-slot:before>
          <q-avatar>
            <img src="https://cdn.quasar.dev/img/avatar5.jpg">
          </q-avatar>
        </template>

        <template v-slot:append>
          <q-icon v-if="text !== ''" name="close" @click="text = ''" class="cursor-pointer" />
          <q-icon name="schedule" />
        </template>

        <template v-slot:hint>
          Field hint
        </template>

        <template v-slot:after>
          <q-btn round dense flat icon="send" @click="wsSend" />
        </template>
      </q-input>
      </q-page-container>

  </q-layout>
</template>

<style scoped>
  .q-virtual-scroll {
    min-width: 600px;
    max-width: 600px;
  }
</style>

<script lang="ts">

import { defineComponent } from '@vue/composition-api'

class MsgObject {
  label = '' as string;
  time = '' as string;
}
export default defineComponent({
  name: 'MainLayout',
  components: { },
  data () {
    return {
      text: '',
      webSocket: new WebSocket('ws://localhost:9999/ws'),
      msgList: [] as MsgObject[],
      dense: false
    }
  },
  created () {
    this.webSocket.onmessage = (ev: MessageEvent<string>) => {
      this.msgList.push({
        label: ev.data,
        time: new Date().toString()
      })
    }
    this.webSocket.onclose = () => {
      this.msgList.push({
        label: 'time out!close!',
        time: new Date().toString()
      })
    }
  },
  methods: {
    wsSend () {
      if (this.text.length > 0) {
        this.webSocket.send(this.text)
        this.text = ''
      }
    }
  }

})
</script>
