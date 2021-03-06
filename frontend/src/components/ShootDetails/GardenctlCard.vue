<!--
Copyright (c) 2019 by SAP SE or an SAP affiliate company. All rights reserved. This file is licensed under the Apache Software License, v. 2 except as noted otherwise in the LICENSE file

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

     http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
-->

<template>
  <v-card v-if="isAdmin">
    <v-card-title class="subheading white--text cyan darken-2 cardTitle">
      Gardenctl
    </v-card-title>
    <div class="list">
      <v-card-title class="listItem" >
        <v-layout class="py-2">
          <v-flex shrink justify-center class="pr-0 pt-3">
            <v-icon class="cyan--text text--darken-2 avatar">mdi-console-line</v-icon>
          </v-flex>
          <v-flex class="pa-0">
            <v-layout row v-for="command in commands" :key="command.title">
              <v-flex>
                <span class="grey--text">{{command.title}}</span><br>
                <code>{{command.value}}</code>
              </v-flex>
              <copy-btn :clipboard-text="command.value"></copy-btn>
            </v-layout>
          </v-flex>
        </v-layout>
      </v-card-title>
    </div>
  </v-card>
</template>

<script>
import CopyBtn from '@/components/CopyBtn'
import { shootItem } from '@/mixins/shootItem'
import { mapState, mapGetters } from 'vuex'
import get from 'lodash/get'

export default {
  components: {
    CopyBtn
  },
  props: {
    shootItem: {
      type: Object
    }
  },
  mixins: [shootItem],
  computed: {
    ...mapState([
      'cfg'
    ]),
    ...mapGetters([
      'projectFromProjectList',
      'isAdmin'
    ]),
    projectName () {
      const project = this.projectFromProjectList
      return get(project, 'metadata.name')
    },
    commands () {
      return [
        {
          title: 'Target Seed',
          value: this.targetSeedCommand
        },
        {
          title: 'Target Shoot',
          value: this.targetShootCommand
        }
      ]
    },
    targetSeedCommand () {
      const args = []
      if (this.cfg.apiServerUrl) {
        args.push(`--server ${this.cfg.apiServerUrl}`)
      }
      if (this.shootSeedName) {
        args.push(`--seed ${this.shootSeedName}`)
      }
      if (this.shootTechnicalId) {
        args.push(`--namespace ${this.shootTechnicalId}`)
      }

      return `gardenctl target ${args.join(' ')}`
    },
    targetShootCommand () {
      const args = []
      if (this.cfg.apiServerUrl) {
        args.push(`--server ${this.cfg.apiServerUrl}`)
      }
      if (this.projectName) {
        args.push(`--project ${this.projectName}`)
      }
      if (this.shootName) {
        args.push(`--shoot ${this.shootName}`)
      }

      return `gardenctl target ${args.join(' ')}`
    }
  }
}
</script>

<style lang="styl" scoped>

  .cardTitle {
    line-height: 10px;
  }

  .listItem {
    padding-top: 0px;
    padding-bottom: 0px;
  }

  .list {
    padding-top: 8px;
    padding-bottom: 8px;
  }

  .avatar {
    padding-right: 33px;
  }

  >>> code {
    box-shadow: none;
    -webkit-box-shadow: none;
  }

</style>
