<template>
  <base-card>
    <section v-if="isCalculating" class="calculator">
      <div class="selects">
        <select
          class="selects__input select-text"
          v-model="selectedBrandId"
          @change="changeBrand($event)"
          :class="{ errorInput: brandError }"
        >
          <option value="" disabled selected>Select a brand</option>
          <option v-for="brand in brands" :key="brand.id" :value="brand.id">
            {{ brand.name }}
          </option>
        </select>
        <select
          class="selects__input select-text"
          v-model="selectedCategoryId"
          @change="changeCategory($event)"
          :class="{ errorInput: categoryError }"
        >
          <option value="" disabled selected>Select a category</option>
          <option
            v-for="category in categories"
            :key="category.id"
            :value="category.id"
          >
            {{ category.name }}
          </option>
        </select>
      </div>
      <div class="size text">
        <p>
          My size is
          <input
            type="text"
            class="size__input"
            v-model="enteredSize"
            @keyup="enterValue()"
            :class="{ errorInput: sizeError }"
          />
          inches.
        </p>
      </div>
      <p v-if="formError" class="error-message">
        Please fill out the fields above
      </p>
      <base-button @click="checkForm()" class="button-text"> CALCULATE </base-button>
    </section>
    <section v-else class="results text">
      <p v-if="noMatch" class="error-message">Sorry, no matching size found</p>
      <div v-else class="results__sizes">
        Your size is
        <div class="results__container">
          <div
            v-for="result in results"
            :key="result.id"
            class="results__content result"
          >
            <p>{{ result.label }} <span>or</span></p>
          </div>
        </div>
      </div>
      <base-button @click="recalculate()" class="button-text"> OK </base-button>
    </section>
  </base-card>
</template>

<script>
import BaseCard from "../components/UI/BaseCard.vue";
import BaseButton from "../components/UI/BaseButton.vue";

export default {
  components: {
    BaseCard,
    BaseButton,
  },
  data() {
    return {
      isCalculating: true,
      selectedBrandId: "",
      selectedCategoryId: "",
      enteredSize: "",
      brands: [],
      categories: [],
      results: "",
      brandError: false,
      categoryError: false,
      sizeError: false,
      formError: false,
      noMatch: false,
    };
  },
  methods: {
    // Fetched brands
    fetchBrands() {
      const username = "user3472";
      const apiKey = "0f6aa487-0f3b-41dc-95be-86c19dd0b98d";
      const promise = fetch("https://size-calculator-api.sspinc.io/brands", {
        mode: "cors",
        credentials: "include",
        method: "GET",
        headers: {
          Authorization: "Basic " + btoa(`${username}:${apiKey}`),
        },
      });
      promise
        .then((response) => response.json())
        .then((data) => {
          this.brands = data.brands;
        });
    },

    // Fetched categories after the brand is selected
    fetchCategories(id) {
      const username = "user3472";
      const apiKey = "0f6aa487-0f3b-41dc-95be-86c19dd0b98d";
      const promise = fetch(
        `https://size-calculator-api.sspinc.io/categories?brand_id=${id}`,
        {
          mode: "cors",
          credentials: "include",
          method: "GET",
          headers: {
            Authorization: "Basic " + btoa(`${username}:${apiKey}`),
          },
        }
      );
      promise
        .then((response) => response.json())
        .then((data) => {
          this.categories = data.categories;
        });
    },

    // Fetched results after everything is selected/entered
    fetchSize(brand_id, category_id, measurement) {
      this.isCalculating = false;
      const username = "user3472";
      const apiKey = "0f6aa487-0f3b-41dc-95be-86c19dd0b98d";
      const promise = fetch(
        `https://size-calculator-api.sspinc.io/sizes?brand_id=${brand_id}&category_id=${category_id}&measurement=${measurement}`,
        {
          mode: "cors",
          credentials: "include",
          method: "GET",
          headers: {
            Authorization: "Basic " + btoa(`${username}:${apiKey}`),
          },
        }
      );
      promise
        .then((response) => response.json())
        .then((data) => {
          this.results = data.sizes;
          if (this.results.length == 0) {
            this.noMatch = true;
          }
        });
    },

    changeBrand() {
      // We fetch the categories only when the brand is selected
      this.fetchCategories(this.selectedBrandId);

      // If a brand is selected, the error disappears
      this.brandError = false;
      this.formError = false;
    },

    changeCategory() {
      // If a category is selected, the error disappears
      this.categoryError = false;
      this.formError = false;
    },

    enterValue() {
      // If a size is entered, the error disappears
      this.sizeError = false;
      this.formError = false;
    },

    checkForm() {
      // If no brand is selected AND no size is entered
      if (this.selectedBrandId == "" && this.enteredSize == "") {
        this.brandError = true;
        this.sizeError = true;

        // If no brand is selected
      } else if (this.selectedBrandId == "") {
        this.brandError = true;

        // If after selecting a brand, there is an array of categories AND no category is selected
      } else if (
        this.categories.length !== 0 &&
        this.selectedCategoryId == ""
      ) {
        this.categoryError = true;

        // If no size is entered
      } else if (this.enteredSize == "") {
        this.sizeError = true;

        // If brand and category are selected and a size is entered
      } else {
        this.brandError = false;
        this.categoryError = false;
        this.sizeError = false;
        this.formError = false;

        // We fetch the results once everything is complete
        this.fetchSize(
          this.selectedBrandId,
          this.selectedCategoryId,
          this.enteredSize
        );
      }

      // If any of the error is true, then form error is true and returns a warning message
      if (this.brandError || this.categoryError || this.sizeError == true) {
        this.formError = true;
      }
    },

    // Reloads the calculator
    recalculate() {
      location.reload();
    },
  },
  mounted() {
    this.fetchBrands();
  },
};
</script>

<style lang="scss">
@import "../styles/app.scss";

.calculator {
  @include align-center();
  flex-direction: column;
  margin: 20px 0 16px;
}

.selects {
  @include align-center();
  flex-direction: column;

  &__input {
    position: relative;
    width: 261px;
    height: 28px;
    padding: 0 0 0 10px;
    background-color: $color-white;
    background-image: url("../assets/dropdown.svg");
    background-repeat: no-repeat;
    background-position-x: 98%;
    background-position-y: 50%;
    border: none;
    border-bottom: 2px solid $color-medium-turquoise;
    color: $color-light-grey;
    cursor: pointer;
    appearance: none;

    &:first-child {
      margin: 0 0 7px 0;
    }
  }
}

.size {
  @include flex-center();
  flex-direction: column;

  &__input {
    width: 66px;
    height: 32px;
    margin: 0 3px;
    border: none;
    background-color: $color-white;
  }
}

.results {
  @include flex-center();
  flex-direction: column;
  height: 189px;

  &__sizes {
    @include flex-center();
    flex-direction: column;
    margin: 0px 0 -20px 0;
  }

  &__container {
    @include justify-center();
    margin: -20px 0px 0px;
  }

  &__content {
    margin: 0 5px;

    &:last-of-type {
      p span {
        display: none;
      }
    }
  }
}

.errorInput {
  border: 1px solid $color-red;
}
</style>
