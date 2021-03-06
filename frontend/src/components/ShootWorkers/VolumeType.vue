<template>
  <hint-colorizer hintColor="orange">
    <v-select
      color="cyan darken-2"
      :items="volumeTypeItems"
      item-text="name"
      item-value="name"
      v-model="worker.volume.type"
      :error-messages="getErrorMessages('worker.volume.type')"
      @input="onInputVolumeType"
      @blur="$v.worker.volume.type.$touch()"
      label="Volume Type"
      :hint="hint"
      persistent-hint>
      <template slot="item" slot-scope="data">
        <v-list-tile-content>
          <v-list-tile-title>{{data.item.name}}</v-list-tile-title>
          <v-list-tile-sub-title v-if="data.item.class">Class: {{data.item.class}}</v-list-tile-sub-title>
        </v-list-tile-content>
      </template>
    </v-select>
  </hint-colorizer>
</template>

<script>

import HintColorizer from '@/components/HintColorizer'
import { required } from 'vuelidate/lib/validators'
import { getValidationErrors } from '@/utils'
import find from 'lodash/find'

const validationErrors = {
  worker: {
    volume: {
      type: {
        required: 'Volume Type is required'
      }
    }
  }
}
const validations = {
  worker: {
    volume: {
      type: {
        required
      }
    }
  }
}

export default {
  components: {
    HintColorizer
  },
  props: {
    worker: {
      type: Object,
      required: true
    },
    volumeTypes: {
      type: Array,
      default: () => []
    }
  },
  data () {
    return {
      validationErrors,
      valid: undefined
    }
  },
  computed: {
    volumeTypeItems () {
      const volumeTypes = this.volumeTypes.slice()
      if (this.notInCloudProfile) {
        volumeTypes.push({
          name: this.worker.volume.type
        })
      }
      this.onInputVolumeType()
      return volumeTypes
    },
    notInCloudProfile () {
      return !find(this.volumeTypes, ['name', this.worker.volume.type])
    },
    hint () {
      if (this.notInCloudProfile) {
        return 'This volume type may not be supported by your worker'
      }
      return undefined
    }
  },
  validations,
  methods: {
    getErrorMessages (field) {
      return getValidationErrors(this, field)
    },
    onInputVolumeType () {
      this.$v.worker.volume.type.$touch()
      this.$emit('updateVolumeType', this.worker.volume.type)
      this.validateInput()
    },
    validateInput () {
      if (this.valid !== !this.$v.$invalid) {
        this.valid = !this.$v.$invalid
        this.$emit('valid', { id: this.worker.id, valid: this.valid })
      }
    }
  },
  mounted () {
    this.$v.$touch()
    this.validateInput()
  }
}
</script>
