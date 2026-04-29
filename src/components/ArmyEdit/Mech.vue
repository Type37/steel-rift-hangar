<script setup>
import {useMechStore} from '../../store/mech-store.js';
import Fraction from '../functional/fraction.vue';
import {computed} from 'vue';
import {BButton, BCollapse} from 'bootstrap-vue-next';
import {useValidationStore} from '../../store/validation-store.js';
import MechStats from './Mech/MechStats.vue';
import HEVCard from '../ArmyPrint/ArmyPrintCards/HEVCard.vue';
import BtnMoveMechToTeam from './Mech/BtnMoveMechToTeam.vue';
import IconValidationError from '../UI/IconValidationError.vue';
import {useTeamStore} from '../../store/team-store.js';
import MechPreferredTeamDropDown from './Mech/MechStats/MechPreferredTeamDropDown.vue';
import IconPreferredTeam from '../UI/IconPreferredTeam.vue';
import {TEAM_SHELF} from '../../data/mech-teams.js';

const mechStore = useMechStore();
const validationStore = useValidationStore();
const teamStore = useTeamStore();

const {
  mechId,
} = defineProps({
  mechId: {
    type: Number,
    required: true,
  },
});

const visible = computed({
  get() {
    return mechStore.getMechVisible(mechId);
  },
  set(val) {
    mechStore.setMechVisible(mechId, val);
  },
});
const info = computed(() => mechStore.getMechInfo(mechId));

const invalidMechMessages = computed(() => validationStore.mechMessages(mechId));
const invalidTeamGroupMessages = computed(() => validationStore.mechTeamGroupMessages(mechId));
const valid = computed(() => !invalidMechMessages.value.length && !invalidTeamGroupMessages.value.length);

const teamId = computed(() => {
  const {teamId} = teamStore.getMechTeamAndGroupIds(mechId);
  return teamId;
});
const teamIcon = computed(() => teamStore.getTeamDef(teamId.value).icon);
const showPreferredTeam = computed(() => teamId.value === TEAM_SHELF);

</script>
<template>
  <div
      :class="{
        'draggable-item': true,
        'card card-mech': true,
        'border-danger': !valid
      }"
  >
    <div class="card-body">
      <div class="mech-row">
        <span class="btn-grab" title="Drag to reorder">:::</span>
        <span class="mech-size">{{ info.size.display_name }}</span>
        <span class="mech-name">
          <IconPreferredTeam btn-class="me-1" :team-id="info.preferred_team_id" :show="showPreferredTeam"/>
          <strong>{{ info.display_name }}</strong>
          <IconValidationError btn-class="ms-1" title="HE-V Validation Errors" icon="hev" :message-array="invalidMechMessages"/>
          <IconValidationError btn-class="ms-1" title="Team Group Validation Errors" :icon="teamIcon" :message-array="invalidTeamGroupMessages"/>
        </span>
        <span class="mech-stats">
          <span>Arm <strong>{{ info.armor_stat }}</strong></span>
          <span>Str <strong>{{ info.structure_stat }}</strong></span>
          <span>Slots <strong><fraction :a="info.used_slots" :b="info.max_slots"/></strong></span>
          <span>Tons <strong><fraction :a="info.used_tons" :b="info.max_tons"/></strong></span>
        </span>
        <span class="mech-actions">
          <BtnMoveMechToTeam :mech-id="mechId"/>
          <button class="mech-btn mech-btn-copy" title="Duplicate" @click="mechStore.duplicateMech(mechId)">
            <span class="material-symbols-outlined">content_copy</span>
          </button>
          <button class="mech-btn mech-btn-delete" title="Delete" @click="mechStore.removeMech(mechId)">
            <span class="material-symbols-outlined">delete</span>
          </button>
          <button
              :class="['mech-btn mech-btn-expand btn-collapse', visible ? null : 'collapsed']"
              :aria-expanded="visible ? 'true' : 'false'"
              :aria-controls="'collapse-' + mechId"
              @click="visible = !visible"
          />
        </span>
      </div>
      <BCollapse
          :id="'collapse-' + mechId"
          v-model="visible"
          lazy
      >
        <hr class="mt-2">
        <div class="d-lg-flex justify-content-lg-center">
          <MechStats :mech-id="mechId"/>
          <div class="output-container ms-3">
            <div class="fw-bold mb-2 pt-2">Card Preview</div>
            <HEVCard :mech-id="mechId" class="shadow"/>
            <MechPreferredTeamDropDown :mech-id="mechId" v-if="teamId === TEAM_SHELF"/>
          </div>
        </div>
      </BCollapse>
    </div>
  </div>
</template>