<template>
  <main class="custom-pd">
    <div class="item secondary">
      <v-card-title>
        <v-icon class="mr-2" color="primary" large
          >mdi-account-circle-outline</v-icon
        >
        Withdraw
        <v-spacer></v-spacer>
        <v-text-field
          v-model="search"
          append-icon="mdi-magnify"
          label="Search"
          single-line
          hide-details
        ></v-text-field>
      </v-card-title>
      <v-data-table :headers="headers" :items="data" :search="search">
        <template v-slot:[`item.created_at`]="{ item }">
          {{ formatDate(item.created_at) }}
        </template>
        <template v-slot:[`item.status`]="{ item }">
          <span v-if="item.status == 'w'" style="color: orange">Waiting</span>
          <span v-if="item.status == 'p'" style="color: #00cdad">Success</span>
          <span v-if="item.status == 'f'" style="color: red">Failed</span> 
        </template>
        <template v-slot:[`item.action`]="{ item }">
          <div v-if="item.status == 'w'">
            <v-btn x-small class="primary mr-1" @click="updateStatus(item.id, 'p')">Success</v-btn>
            <v-btn x-small class="red" @click="updateStatus(item.id, 'f')">Failed</v-btn>
          </div>
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
      search: "",
      dialog: false,
      headers: [
        { text: "UID", value: "uid" },
        { text: "Số tiền rút", value: "amount" },
        { text: "Thời gian", value: "created_at" },
        { text: "Trạng thái", value: "status" },
        { text: "Hành động", value: "action" },
      ],
      data: [],
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
      this.CallAPI("get", "history", {}, (res) => {
        this.data = res.data;
      });
    },
    updateStatus(id, value){
      this.CallAPI("put", "history/" + id, {status: value}, (res) => {
        this.getData()
      });
    },  
    formatDate(date) {
      return date.slice(0, 10);
    },
  },
};
</script>