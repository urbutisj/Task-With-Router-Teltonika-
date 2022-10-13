<template>
  <VuciForm :uci-config="config" v-if="showModal">
    <VuciNamedSection :name="modalData" v-slot="{s}" :card="false">
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
      protocols: ['dhpc', 'static']
    }
  }
}
</script>
