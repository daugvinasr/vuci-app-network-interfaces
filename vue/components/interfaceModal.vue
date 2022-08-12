<template>
  <div>
    <a-modal :title="'Interface: '+ interfaceName" v-model="$parent.modalVisibility" :width="500" style="">
    <vuci-form uci-config="network_interfaces" @applied="$parent.handleSubmit()">
        <vuci-named-section type="interface" :name="sid" :label="'Network interface'" :collapsible="false" v-slot="{ s }" :card="false">
          <vuci-form-item-select :uci-section="s" :label="'Protocol'" name="protocol" initial="none" :options="protocols"/>
          <vuci-form-item-input :uci-section="s" :label="'Address'" name="address" rules="ip4addr"/>
          <vuci-form-item-input :uci-section="s" :label="'Netmask'" name="netmask" rules="netmask4"/>
          <vuci-form-item-input :uci-section="s" :label="'Gateway'" name="gateway" rules="ip4addr"/>
          <vuci-form-item-list :uci-section="s" :label="'DNS'" name="dns" rules="ip4addr"/>
        </vuci-named-section>
      </vuci-form>
        <template #footer><div></div></template>
    </a-modal>
  </div>
</template>

<script>
export default {
  props: { sid: String, interfaceName: String },
  data () {
    return {
      protocols: [
        ['none', 'Unmanaged'],
        ['dhcp', 'DHCP Client'],
        ['static', 'Static address'],
        ['pppoe', 'PPPoE'],
        ['pptp', 'PPtP'],
        ['l2tp', 'L2TP'],
        ['3g', '3G']
      ]
    }
  }
}
</script>
