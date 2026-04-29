<script setup>
import BtnLoad from './UI/BtnLoad.vue';
import BtnSave from './UI/BtnSave.vue';
import {useArmyListStore} from '../store/army-list-store.js';
import {storeToRefs} from 'pinia';
import {useTeamStore} from '../store/team-store.js';
import {useSupportAssetCountsStore} from '../store/support-asset-count-store.js';
import {ref} from 'vue';
import {ROUTE_HOME, ROUTE_PRINT} from '../router.js';
import Fraction from './functional/fraction.vue';
import {resetStores} from '../store/helpers/store-save-load.js';
import {BButton, BModal} from 'bootstrap-vue-next';
import BtnArmyListValidation from './ArmyEdit/ArmyList/BtnArmyListValidation.vue';

const {used_teams_count, max_teams_count} = storeToRefs(useTeamStore());
const {used_support_assets, max_support_assets} = storeToRefs(useSupportAssetCountsStore());
const {used_tons, max_tons, name} = storeToRefs(useArmyListStore());
const resetModal = ref(false);
</script>

<template>
  <header class="app-header">
    <div class="app-header-inner">
      <input type="text" v-model="name" placeholder="New Army" class="app-header-name" aria-label="Army name"/>
      <div class="app-header-stats">
        <span>Teams: <strong><Fraction :a="used_teams_count" :b="max_teams_count"/></strong></span>
        <span>Support: <strong><Fraction :a="used_support_assets" :b="max_support_assets"/></strong></span>
        <span>Tonnage: <strong><Fraction :a="used_tons" :b="max_tons"/>T</strong></span>
        <BtnArmyListValidation/>
      </div>
      <div class="app-header-actions">
        <BtnSave/>
        <BtnLoad/>
        <BButton size="sm" variant="outline-danger" @click="resetModal = true">New List</BButton>
        <div class="btn-group" role="group">
          <router-link :to="{ name: ROUTE_HOME }" class="btn btn-sm btn-outline-secondary" active-class="active">Edit</router-link>
          <router-link :to="{ name: ROUTE_PRINT }" class="btn btn-sm btn-outline-secondary" active-class="active">Print</router-link>
        </div>
      </div>
    </div>
  </header>

  <BModal v-model="resetModal" centered @ok="resetStores()" ok-variant="danger" title="Start New List?">
    <div class="lead">Start a new blank list? Any unsaved changes will be lost.</div>
  </BModal>
</template>
