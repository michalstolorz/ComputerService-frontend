<template>
  <v-row justify="center">
    <v-dialog v-model="dialog" persistent max-width="800px">
      <template v-slot:activator="{ on, attrs }">
        <v-btn color="green accent-4" dark v-bind="attrs" v-on="on">
          Add Part
        </v-btn>
      </template>
      <v-card>
        <v-card-title>
          <span class="headline">Add Part</span>
        </v-card-title>
        <v-card-text>
          <v-container>
            <v-row>
              <v-col cols="12">
                <v-row
                  v-show="emptyErrorFlag"
                  v-for="error in errorList"
                  :key="error.id"
                  style="color: red; font-size: 100%; font-weight: 1500"
                >
                  {{ error.name }}
                </v-row>
                <v-row
                  v-show="error500Flag"
                  style="color: red; font-size: 100%; font-weight: 1500"
                >
                  Part with given name already exist.
                </v-row>
                <v-text-field
                  v-model="partName"
                  :error-messages="partNameErrors"
                  label="Part Name"
                  required
                  @input="$v.partName.$touch()"
                  @blur="$v.partName.$touch()"
                ></v-text-field>
                <v-text-field
                  v-model="quantity"
                  :error-messages="quantityErrors"
                  label="Quantity"
                  required
                  @input="$v.quantity.$touch()"
                  @blur="$v.quantity.$touch()"
                ></v-text-field>
                <v-text-field
                  v-model="partBoughtPrice"
                  :error-messages="partBoughtPriceErrors"
                  label="Part Bought Price"
                  required
                  @input="$v.partBoughtPrice.$touch()"
                  @blur="$v.partBoughtPrice.$touch()"
                ></v-text-field>
              </v-col>
            </v-row>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="dialog = !dialog">
            Close
          </v-btn>
          <v-btn
            color="blue darken-1"
            text
            @click="dialog = !dialog"
            v-on:click="post"
          >
            Add
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-row>
</template>

<script>
import { validationMixin } from "vuelidate";
import { required, minValue } from "vuelidate/lib/validators";

export default {
  mixins: [validationMixin],

  validations: {
    partName: { required },
    quantity: { required, minValue: minValue(1) },
    partBoughtPrice: { required, minValue: minValue(1) },
  },

  data() {
    return {
      dialog: false,
      partName: "",
      quantity: 0,
      partBoughtPrice: 0,
      error500Flag: false,
      emptyErrorFlag: false,
      errorList: [],
    };
  },
  computed: {
    partNameErrors() {
      const errors = [];
      if (!this.$v.partName.$dirty) return errors;
      !this.$v.partName.required && errors.push("Part name is required");
      return errors;
    },
    quantityErrors() {
      const errors = [];
      if (!this.$v.quantity.$dirty) return errors;
      !this.$v.quantity.required && errors.push("Quantity is required");
      !this.$v.quantity.minValue &&
        errors.push("Quantity can not be 0 or less");
      return errors;
    },
    partBoughtPriceErrors() {
      const errors = [];
      if (!this.$v.partBoughtPrice.$dirty) return errors;
      !this.$v.partBoughtPrice.required &&
        errors.push("Part bought price is required");
      !this.$v.partBoughtPrice.minValue &&
        errors.push("Part bought price can not be 0 or less");
      return errors;
    },
  },
  methods: {
    fillPartNameErrors: function () {
      let errors = "";
      if (!this.$v.partName.$dirty) return errors;
      if (!this.$v.partName.required) return (errors = "Part name is required");
      return errors;
    },
    fillQuantityErrors: function () {
      let errors = "";
      if (!this.$v.quantity.$dirty) return errors;
      if (!this.$v.quantity.required) return (errors = "Quantity is required");
      if (!this.$v.quantity.minValue)
        return (errors = "Quantity can not be 0 or less");
      return errors;
    },
    fillPartBoughtPriceErrors: function () {
      let errors = "";
      if (!this.$v.partBoughtPrice.$dirty) return errors;
      if (!this.$v.partBoughtPrice.required)
        return (errors = "Part bought price is required");
      if (!this.$v.partBoughtPrice.minValue)
        return (errors = "Part bought price can not be 0 or less");
      return errors;
    },
    fillErrorList: function () {
      this.errorList = [];
      if (this.fillPartNameErrors() !== "")
        this.errorList.push({ id: 1, name: this.fillPartNameErrors() });
      if (this.fillQuantityErrors() !== "")
        this.errorList.push({ id: 2, name: this.fillQuantityErrors() });
      if (this.fillPartBoughtPriceErrors() !== "")
        this.errorList.push({ id: 3, name: this.fillPartBoughtPriceErrors() });
      this.emptyErrorFlag = true;
      this.error500Flag = false;
    },
    post: function () {
      this.$v.$touch();
      if (this.$v.$invalid) {
        this.dialog = true;
        this.fillErrorList();
      } else {
        this.$http
          .post("https://localhost:44308/api/Part/addPart", {
            name: this.partName,
            quantity: this.quantity,
            partBoughtPrice: this.partBoughtPrice,
          })
          .then(
            function () {
              location.reload();
            },
            function (error) {
              console.log(error);
              this.dialog = true;
              this.error500Flag = true;
              this.emptyErrorFlag = false;
            }
          );
      }
    },
  },
};
</script>