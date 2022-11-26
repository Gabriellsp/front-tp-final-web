<template>
  <div class="restricted-area">
    <Navdrawer @changeTab="changedTab($event)"></Navdrawer>
    <DoctorTab v-if="activeTab == 'Médicos'"></DoctorTab>
    <RegisterScheduleTab
      v-if="activeTab == 'Cadastrar Agendamento'"
    ></RegisterScheduleTab>
    <SchedulesTab v-if="activeTab == 'Agendamentos'"></SchedulesTab>
    <RegisterAddressTab
      v-if="activeTab == 'Cadastrar Endereço'"
    ></RegisterAddressTab>
    <AddressesTab v-if="activeTab == 'Endereços'"></AddressesTab>
  </div>
</template>

<script>
import Navdrawer from "@/components/Navdrawer";
import DoctorTab from "@/views/RestrictedAreaTabs/Doctors";
import RegisterScheduleTab from "@/views/RestrictedAreaTabs/RegisterSchedule";
import SchedulesTab from "@/views/RestrictedAreaTabs/Schedules";
import RegisterAddressTab from "@/views/RestrictedAreaTabs/RegisterAddress";
import AddressesTab from "@/views/RestrictedAreaTabs/Addresses";
export default {
  components: {
    Navdrawer,
    DoctorTab,
    RegisterScheduleTab,
    SchedulesTab,
    RegisterAddressTab,
    AddressesTab,
  },
  methods: {
    changedTab(activeTab) {
      this.activeTab = activeTab;
    },
  },
  data() {
    return {
      activeTab: "Funcionários",
    };
  },
};
</script>
