<template>
  <div>
    <vuci-form uci-config="network_interfaces" :key="reRenderTable">
      <vuci-typed-section type="interface" :columns="columns">
        <template #name="{ s }">
          <vuci-form-item-dummy :uci-section="s" name="name" />
        </template>
        <template #address="{ s }">
          <vuci-form-item-dummy :uci-section="s" name="address" />
        </template>
        <template #netmask="{ s }">
          <vuci-form-item-dummy :uci-section="s" name="netmask" />
        </template>
        <template #buttons="{ s }">
          <a-button type="primary" @click="handleEdit(s['.name'], s.name, s.protocol)" v-text="'Edit'"
            style="margin-right: 5px"></a-button>
          <a-button type="danger" @click="showDelete(s['.name'], s.name)" v-text="'Delete'" style="margin-right: 5px">
          </a-button>
        </template>
      </vuci-typed-section>
      <template #footer>
        <div style="">
          <div style="display: flex">
            <label style="margin-right: 5px; padding-top: 5px;">Interface name: </label>
            <a-input @pressEnter="newInterfaceName.length != 0 ? handleAdd() : null" v-model="newInterfaceName"
              style="margin-right: 5px; width: 200px" required></a-input>
            <a-button @click="newInterfaceName.length != 0 ? handleAdd() : null" type="primary" v-text="'Create'">
            </a-button>
          </div>
        </div>
      </template>
    </vuci-form>
    <div>
      <edit-modal :key="reRenderModal" :sid="currentSid" :interfaceName="selectedInterfaceName"></edit-modal>
    </div>
  </div>
</template>
<script>
import editModal from './components/interfaceModal.vue'
export default {
  components: { editModal },
  data () {
    return {
      modalVisibility: false,
      selectedInterfaceName: '',
      newInterfaceName: '',
      reRenderTable: false,
      reRenderModal: false,
      showAdditionalFields: false,
      currentSid: '',
      columns: [
        { name: 'name', label: 'Interface name' },
        { name: 'address', label: 'Address' },
        { name: 'netmask', label: 'Netmask' },
        { name: 'buttons', label: 'Actions' }
      ]
    }
  },
  methods: {
    async handleAdd () {
      this.$spin()
      const sid = this.$uci.add('network_interfaces', 'interface')
      await this.$uci.set('network_interfaces', sid, 'name', this.newInterfaceName)
      await this.$uci.save()
      this.findLatestSid()
        .then(response => {
          this.currentSid = response
          this.selectedInterfaceName = this.newInterfaceName
          this.newInterfaceName = ''
          this.handleModalRefresh()
          this.modalVisibility = true
        })
      this.showAdditionalFields = false
      this.$spin(false)
    },
    async findLatestSid () {
      await this.$uci.load('network_interfaces')
      const data = this.$uci.sections('network_interfaces')
      return data[data.length - 1]['.name']
    },
    showDelete (sid, name) {
      this.$confirm({
        content: 'Are you sure you want to delete ' + name + ' interface?',
        onOk: () => {
          this.handleDelete(sid)
        }
      })
    },
    async handleDelete (sid) {
      this.$spin()
      await this.$uci.del('network_interfaces', sid)
      await this.$uci.save()
      this.handleTableRefresh()
      this.$spin(false)
    },
    handleEdit (sid, name, protocol) {
      this.selectedInterfaceName = name
      this.currentSid = sid
      this.handleModalRefresh()
      if (protocol === 'static') {
        this.showAdditionalFields = true
      } else {
        this.showAdditionalFields = false
      }
      this.modalVisibility = true
    },
    handleTableRefresh () {
      this.reRenderTable = !this.reRenderTable
    },
    handleModalRefresh () {
      this.reRenderModal = !this.reRenderModal
    },
    handleSubmit () {
      this.handleTableRefresh()
      this.modalVisibility = false
    }
  }
}
</script>
