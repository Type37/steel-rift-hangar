<script setup>
import {storeToRefs} from 'pinia';
import {useTeamStore} from '../../../store/team-store.js';
import MechTeam from '../MechTeam/MechTeam.vue';
import {computed} from 'vue';
import {TEAM_SHELF, TEAM_GENERAL} from '../../../data/mech-teams.js';
import MechTeamGroup from '../MechTeam/MechTeamGroup.vue';
import BtnAddTeam from '../../UI/BtnAddTeam.vue';
import {BButton} from 'bootstrap-vue-next';

const teamStore = useTeamStore();
const {special_teams} = storeToRefs(teamStore);
const baseMechCount = computed(() => teamStore.getTeamMechCount(TEAM_GENERAL) || teamStore.getTeamMechCount(TEAM_SHELF));
const shelfCount = computed(() => teamStore.getTeamMechCount(TEAM_SHELF));

function addGeneralMech() {
  teamStore.addMechToTeamWithDefaults(TEAM_GENERAL, 'A');
}
</script>
<template>
  <div class="action-bar">
    <BButton variant="primary" class="add-hev-cta" @click="addGeneralMech">
      <Icon name="hev" size="20px" class="me-1"/>
      Add Solo HE-V
    </BButton>
    <BtnAddTeam/>
    <slot name="support-assets"/>
  </div>

  <div class="mech-workspace" :class="{'has-shelf': shelfCount}">
    <div class="mech-main">
      <MechTeamGroup :team-id="TEAM_GENERAL" group-id="A" v-show="baseMechCount"/>
      <MechTeam v-for="team in special_teams" :key="team.id" :team-id="team.id"/>
      <div class="d-flex justify-content-end mt-2" v-if="special_teams.length">
        <BtnAddTeam/>
      </div>
    </div>
    <div class="mech-shelf" v-if="shelfCount">
      <MechTeamGroup :team-id="TEAM_SHELF" group-id="A"/>
    </div>
  </div>
</template>
