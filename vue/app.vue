<template>
  <div>
    <VuciForm :key="counter" :uci-config="config">
        <VuciTypedSection :type="section" :columns="columns">
            <template #name="{ s }">
              <VuciFormItemDummy :uci-section="s" name="name" />
            </template>
            <template #address="{ s }">
              <VuciFormItemDummy :uci-section="s" name="address"/>
            </template>
            <template #netmask="{ s }">
              <VuciFormItemDummy :uci-section="s" name="netmask"/>
            </template>
            <template #action="{s}">
              <a-button type="primary" :uci-section="s" size="small" style="margin-right: 0.4rem" @click="handleEdit(s['.name'])">Edit</a-button>
              <a-popconfirm @confirm="del(s['.name'])" placement="left" :title="'Delete this interface?'">
            <a-button type="danger" size="small" :uci-section="s">Delete</a-button>
          </a-popconfirm>
            </template>
        </VuciTypedSection>
        <template #footer>
          <a-input placeholder="New Interface Name..." v-model="nIntName" id="interface-name" />
          <a-button type="primary" @click="handleAdd">Create</a-button>
        </template>
    </VuciForm>
    <a-modal v-model="modalVisible">
      <SectionModal :modalData="sectionToEdit" :showModal="modalVisible"/>
      <template #footer></template>
    </a-modal>
  </div>
</template>

<script>
import SectionModal from '../interfaces/SectionModal.vue'
export default {
  components: { SectionModal },
  data () {
    return {
      config: 'task_config',
      section: 'interface',
      sections: [],
      nIntName: '',
      columns: [
        { name: 'name', label: 'Interface Name' },
        { name: 'address', label: 'Address' },
        { name: 'netmask', label: 'Netmask' },
        { name: 'action', label: '' }
      ],
      modalVisible: false,
      sectionToEdit: undefined,
      counter: 1,
      protocol: 'protocol'
    }
  },
  methods: {
    async handleEdit (sid) {
      this.sectionToEdit = sid
      this.modalVisible = true
    },
    async del (name) {
      this.$spin()
      await this.$uci.load(this.config)
      this.$uci.del(this.config, name)
      this.$uci.save().then(() => {
        this.$uci.apply().then(() => {
          this.load()
          this.$spin(false)
        })
      })
    },
    handleAdd () {
      if (!this.nIntName) return
      for (let i = 0; i < this.sections.length; i++) {
        if (this.sections[i].name === this.nIntName) {
          this.$message.error('Name already used')
          return
        }
      }
      this.createSection()
    },
    async load () {
      await this.$uci.load(this.config)
      this.sections = this.$uci.sections(this.config, this.section)
      this.counter++
    },

    async createSection () {
      this.$spin()
      this.$uci.add(this.config, this.section)
      const sections = this.$uci.sections(this.config, this.section)
      const lastIndex = sections.length - 1
      const sid = sections[lastIndex]['.name']
      this.$uci.set(this.config, sid, 'name', this.nIntName)
      this.$uci.set(this.config, sid, 'protocol', 'static')
      await this.$uci.save().then(() => {
        this.$uci.apply()
        this.load()
        this.$spin(false)
      })
      this.sectionId = ''
      this.sectionId = await this.getLastSectionId()
      this.counter++
      this.handleEdit(this.sectionId)
      this.nIntName = ''
    },
    async getLastSectionId () {
      await this.$uci.load(this.config)
      const sections = this.$uci.sections(this.config, this.section)
      const lastSectionIndex = sections.length - 1
      return sections[lastSectionIndex]['.name']
    }
  },
  watch: {
    editModal () {
      this.load()
    }
  }
}
</script>

<style>
#interface-name {
  width: 200px;
  margin-right: .5rem;
}
</style>
