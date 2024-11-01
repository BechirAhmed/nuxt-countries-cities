<template>
  <div class="">
    <select
      v-if="!onlyCity"
      class="w-full p-2 border border-gray-200 rounded"
      v-model="localSelectedCountry"
    >
      <option value="" selected disabled>{{ countryLabel }}</option>
      <option :value="country.name" v-for="country of countryList" :key="country.id">
        {{ country.name }}
      </option>
    </select>
    <select
      v-if="!onlyCountry"
      class="w-full p-2 border border-gray-200 rounded"
      v-model="selectedCity"
    >
      <option disabled v-if="!localSelectedCountry.length">Please select a country first!</option>
      <option disabled v-if="!cityList.length && localSelectedCountry.length">
        Sorry, there is no list of cities in {{ localSelectedCountry }}!
      </option>
      <option :value="city.name" v-for="city in cityList" :key="city.id">
        {{ city.name }}
      </option>
    </select>
  </div>
</template>

<script>
export default {
  name: "nuxtCountriesCities",
  props: {
    onlyCountry: {
      type: Boolean,
      default: false, // Default to false, meaning show country select unless specified
    },
    onlyCity: {
      type: Boolean,
      default: false, // Default to false, meaning show city select unless specified
    },
    selectedCountry: {
      type: String,
      default: "",
    },
    countryLabel: {
      type: String,
      default: "Select the country",
    },
  },
  data() {
    return {
      countryList: [],
      cityList: [],
      selectedCity: "",
      localSelectedCountry: this.selectedCountry
    };
  },
  created() {
    import("./data/countries.json").then((data) => {
      this.countryList = data.default || data;
    });
  },
  watch: {
    selectedCountry(newVal) {
      this.localSelectedCountry = newVal; // Update local property when prop changes
      this.updateCityList(newVal); // Update city list based on the new selected country
    },
    localSelectedCountry(newVal) {
      this.$emit("country", newVal); // Emit the new selected country
      this.updateCityList(newVal); // Update the city list based on the new local country selection
    },
    selectedCity() {
      this.$emit("city", this.selectedCity);
    },
  },
  methods: {
    updateCityList(country) {
      this.cityList = []; // Clear the city list
      const selectedCountry = this.countryList.find(c => c.name === country);
      if (selectedCountry) {
        this.cityList = selectedCountry.states; // Set the city list based on the selected country
      }
    },
  },
};
</script>

<style lang="scss" scoped>

</style>
