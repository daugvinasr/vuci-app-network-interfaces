<template>
  <div>
    <a-modal :title="'Interface: ' + interfaceName" v-model="$parent.modalVisibility" :width="500" style="">
      <vuci-form uci-config="network_interfaces" @applied="$parent.handleSubmit(sid, interfaceName)">
        <vuci-named-section type="interface" :name="sid" :label="'Network interface'" :collapsible="false"
          v-slot="{ s }" :card="false">
          <vuci-form-item-select :uci-section="s" :label="'Protocol'" name="protocol" initial="None"
            :options="protocols" @change="showAdditionalInformation" :required="true" />
          <div v-if="$parent.selectedProtocol === 'static'">
            <vuci-form-item-input :uci-section="s" :label="'Address'" name="address" rules="ip4addr" :required="true"/>
            <vuci-form-item-input :uci-section="s" :label="'Netmask'" name="netmask" rules="netmask4" :required="true" />
            <vuci-form-item-input :uci-section="s" :label="'Gateway'" name="gateway" rules="ip4addr" :required="true" />
            <vuci-form-item-list :uci-section="s" :label="'DNS'" name="dns" rules="ip4addr" />
          </div>
        </vuci-named-section>
      </vuci-form>
      <template #footer>
        <div></div>
      </template>
    </a-modal>
  </div>
</template>

<script>
export default {
  props: { sid: String, interfaceName: String },
  data () {
    return {
      protocols: [
        ['static', 'Static address'],
        ['dhcp', 'DHCP Client']
      ]
    }
  },
  methods: {
    showAdditionalInformation (self) {
      this.$parent.selectedProtocol = self.model
    }
  }
}
</script>
