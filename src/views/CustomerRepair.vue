<template>
  <v-container>
    <v-row>
      <v-col cols="12" sm="4">
        <div class="centerDiv">Create Date Time:</div>
        <div class="centerDiv">
          {{ repair.createDateTime | formatDate }}
        </div>
      </v-col>
      <v-col cols="12" sm="4"
        ><div class="centerDiv">
          <div>STATUS</div>
          <v-btn
            v-if="repair.status == 1"
            x-large
            color="light-blue accent-4"
            dark
          >
            New
          </v-btn>
          <v-btn v-if="repair.status == 2" x-large color="orange" dark>
            For Customer Approval
          </v-btn>
          <v-btn
            v-if="repair.status == 3"
            x-large
            color="deep-purple accent-4"
            dark
          >
            In Progress
          </v-btn>
          <v-btn v-if="repair.status == 4" x-large color="success" dark>
            Finished
          </v-btn>
        </div>
      </v-col>
      <v-col cols="12" sm="4">
        <div class="centerDiv">Finish Date Time:</div>
        <div v-if="repair.finishDateTime === null" class="centerDiv">
          Not finished yet
        </div>
        <div class="centerDiv" v-else>
          {{ repair.finishDateTime | formatDate }}
        </div>
      </v-col>
    </v-row>

    <br />
    <v-row>
      <v-col cols="12" sm="4">
        <v-hover v-slot:default="{ hover }" disabled>
          <v-card
            :elevation="hover ? 16 : 2"
            :class="{ 'on-hover': hover }"
            class="mx-auto"
            height="200"
            max-width="350"
          >
            <v-card-text class="text-center title">
              Customer info:
              <br />
              {{ repair.customerFirstName }} {{ repair.customerLastName }}
              {{ repair.customerEmail }}
              {{ repair.customerPhoneNumber }}
            </v-card-text>
          </v-card>
        </v-hover>
      </v-col>
      <v-col cols="12" sm="4">
        <div class="centerDiv">
          Repair Costs:
          <br />
          {{ repair.repairCost }} zł
        </div>
      </v-col>
      <v-col cols="12" sm="4">
        <v-hover v-slot:default="{ hover }" disabled>
          <v-card
            :elevation="hover ? 16 : 2"
            :class="{ 'on-hover': hover }"
            class="mx-auto"
            height="200"
            max-width="350"
          >
            <v-card-text class="text-center title">
              Assign Employees:
              <div
                v-for="repairUser in repair.repairUsers"
                :key="repairUser.userId"
              >
                {{ repairUser.userFirstName + " " + repairUser.userLastName }}
              </div>
            </v-card-text>
          </v-card>
        </v-hover>
      </v-col>
    </v-row>

    <v-row>
      <v-col cols="12" sm="4"></v-col>
      <v-col cols="12" sm="4"></v-col>
    </v-row>

    <v-row>
      <v-col cols="12" sm="4">
        <v-hover v-slot:default="{ hover }" disabled>
          <v-card
            :elevation="hover ? 16 : 2"
            :class="{ 'on-hover': hover }"
            class="mx-auto"
            height="350"
            max-width="500"
          >
            <v-card-text class="text-center title">
              Description:
              <br />
              {{ repair.description }}
            </v-card-text>
          </v-card>
        </v-hover>
      </v-col>

      <v-col cols="12" sm="4">
        <v-hover v-slot:default="{ hover }" disabled>
          <v-card
            :elevation="hover ? 16 : 2"
            :class="{ 'on-hover': hover }"
            class="mx-auto"
            height="350"
            max-width="500"
          >
            <v-card-text class="text-center title">
              Used Parts:
              <div
                class="text-center title"
                v-for="part in repair.usedParts"
                :key="part.id"
              >
                {{ part.name }} - {{ part.quantity }}
              </div>
            </v-card-text>
          </v-card>
        </v-hover>
      </v-col>

      <v-col cols="12" sm="4">
        <v-hover v-slot:default="{ hover }" disabled>
          <v-card
            :elevation="hover ? 16 : 2"
            :class="{ 'on-hover': hover }"
            class="mx-auto"
            height="350"
            max-width="500"
          >
            <v-card-text class="text-center title">
              Repair Types:
              <div
                class="text-center title"
                v-for="repairType in repair.repairTypes"
                :key="repairType.id"
              >
                {{ repairType.name }}
              </div>
            </v-card-text>
          </v-card>
        </v-hover>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  components: {},

  data() {
    return {
      repairId: this.$route.params.id,
      repair: {},
    };
  },
  created() {
    this.$http
      .get(
        "https://localhost:44308/api/Repair/getCustomerRepair/" + this.repairId
      )
      .then((data) => {
        this.repair = data.body;
      });
  },
  methods: {},
};
</script>

<style>
.centerDiv {
  text-align: center;
  font-size: xx-large;
  font-weight: 500;
}
</style>