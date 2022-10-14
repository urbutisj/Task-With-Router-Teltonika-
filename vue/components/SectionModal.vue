<template>
  <VuciForm :uci-config="config" v-if="showModal">
    <VuciNamedSection :name="modalData" v-slot="{s}" @change-protocol="checkProtocol" :card="false">
        <VuciFormItemDummy :uci-section="s" :label="'Name'" value="nIntName" name="name" />
        <VuciFormItemSelect :uci-section="s" :label="'Protocol'" :options="protocols" initial="static" name="protocol" required/>
        <VuciFormItemInput :uci-section="s" :label="'IP address'" placeholder="192.168.1.1" depend="protocol == 'static'" name="address" rules="ipaddr" required/>
        <VuciFormItemInput :uci-section="s" :label="'Netmask'" placeholder="255.255.255.0" depend="protocol == 'static'" name="netmask" rules="netmask4" required/>
        <VuciFormItemInput :uci-section="s" :label="'Gateway'" placeholder="192.168.1.254" depend="protocol == 'static'" name="gateway" rules="ipaddr"/>
        <VuciFormItemList :uci-section="s" :label="'DNS'"  placeholder="8.8.8.8" depend="protocol == 'static'" name="dns" required/>
    </VuciNamedSection>
    </VuciForm>
</template>

<script>
export default {
  props: {
    modalData: String,
    showModal: Boolean
  },
  data () {
    return {
      config: 'task_config',
      section: 'interface',
      protocols: ['dhcp', 'static']
    }
  },
  methods: {
    checkProtocol (self) {
      if (self.model === 'dhcp') {
        this.$uci.set(this.config, this.modalData, 'address', '')
        this.$uci.set(this.config, this.modalData, 'gateway', '')
        this.$uci.set(this.config, this.modalData, 'dns', '')
        this.$uci.set(this.config, this.modalData, 'netmask', '')
        // Workaround
        this.$uci.set(this.config, this.modalData, 'workaround', '')
      } else {
        this.$uci.reset()
      }
    }
  }
}
</script>

<style>
</style>
