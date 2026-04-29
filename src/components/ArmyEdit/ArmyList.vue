<script setup>
import Faction from './ArmyList/Faction.vue';
import ArmyListSize from './ArmyList/ArmyListSize.vue';
import ArmyListSupportAssetCount from './ArmyList/ArmyListSupportAssetCount.vue';
import ArmyListTeams from './ArmyList/ArmyListTeams.vue';
</script>
<template>
  <div class="army-config-bar">
    <div class="army-config-cards">
      <ArmyListSize/>
      <div class="config-card">
        <ArmyListSupportAssetCount/>
      </div>
      <div class="config-card">
        <ArmyListTeams/>
      </div>
    </div>
    <div class="army-config-faction">
      <Faction/>
    </div>
  </div>
</template>
