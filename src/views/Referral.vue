<template>
  <main class="custom-pd">
    <div class="item secondary">
      <v-card-title>
        <v-icon class="mr-2" color="primary" large
          >mdi-account-circle-outline</v-icon
        >
        Referral
        <v-spacer></v-spacer>
        <v-text-field
          v-model="search"
          append-icon="mdi-magnify"
          label="Search"
          single-line
          hide-details
        ></v-text-field>
      </v-card-title>
      <v-data-table :headers="headers" :items="data" :search="search" :sort-desc="true"
      sort-by="refund">
        <template v-slot:[`item.inviteTime`]="{ item }">
          {{ formatDate(item.inviteTime) }}
        </template>
        <template v-slot:[`item.refund`]="{ item }">
          {{ refundTotal(item.refund)}}
        </template>
        <template v-slot:[`item.balance`]="{ item }">
          {{ (refundTotal(item.refund) - item.withdraw).toFixed(3)}}
        </template>
      </v-data-table>
    </div>
  </main>
</template>

<script>
import { mapGetters } from "vuex";
export default {
  data() {
    return {
      percent: 0.5,
      search: "",
      dialog: false,
      headers: [
        { text: "UID", value: "uid" },
        { text: "Tham gia", value: "inviteTime" },
        { text: "Tổng hoàn phí", value: "refund" },
        { text: "Đã rút", value: "withdraw" },
        { text: "Số dư", value: "balance" },
      ],
      data: [],
      edit_id: "",
      content: "",
    };
  },
  computed: {
    ...mapGetters(["account"]),
  },
  mounted() {
    this.getData();
  },
  methods: {
    getData() {
      this.CallAPI("get", "ref/cm", {}, (res) => {
        this.data = res.data      
      });
    },
    editItem(item) {
      this.edit_id = item.id;
      this.dialog = true;
    },
    formatDate(date) {
      return new Date(date*1000).toLocaleDateString()
    },
    refundTotal(value){
      return (value*this.percent).toFixed(3)
    }
  },
};
</script>