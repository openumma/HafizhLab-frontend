<template>
  <div>
    <div class="memo-surah-title pb-3 pt-3">
      <h4 class="mt-2 text-center">
        {{ surah.name }}
      </h4>
    </div>
    <b-container>
      <b-row class="mb-2">
        <b-col><h5 class="progress-header my-auto">Memorized</h5></b-col>
        <b-col>
          <h6 class="progress-header text-right my-auto">
            {{ memo_value }} / {{ max }}
          </h6>
        </b-col>
      </b-row>
      <b-progress
        :value="memo_value"
        :max="max"
        height="18px"
        class="memo-progress-bar"
      ></b-progress>
      <div class="mt-3 mb-4">
        <b-row class="mb-2">
          <b-col><h5 class="progress-header my-auto">Tested</h5></b-col>
          <b-col>
            <h6 class="progress-header text-right my-auto">
              {{ test_value }} / {{ max }}
            </h6>
          </b-col>
        </b-row>
        <b-progress
          :value="test_value"
          :max="max"
          height="18px"
          class="test-progress-bar"
        ></b-progress>
      </div>
      <b-container class="text-center">
        <b-button v-b-modal.modal-1 class="memorize-button" size="lg">
          Mark Memorized Verses
        </b-button>
        <b-modal
          id="modal-1"
          ref="modal-memo"
          title="Mark Memorized Verses"
          hide-footer="true"
        >
          <b-form @submit="onSubmit">
            <b-form-group
              id="input-group-from"
              label="From:"
              label-for="input-from"
            >
              <b-form-select
                id="input-from"
                v-model="form.versefrom"
                :options="verses"
                required
              ></b-form-select>
            </b-form-group>
            <b-form-group id="input-group-to" label="To:" label-for="input-to">
              <b-form-select
                id="input-to"
                v-model="form.verseto"
                :options="verses"
                required
              ></b-form-select>
            </b-form-group>
            <b-button type="submit" variant="primary">Submit</b-button>
          </b-form>
        </b-modal>
      </b-container>
      <hr />
      <b-list-group v-for="(ayat, index) in ayatData" :key="ayat">
        <b-list-group-item class="ayat-items">
          <b-row>
            <b-col align-self="center" class="text-left pl-0">
              <b-button
                :id="'button' + index"
                class="check-verse-button py-0 white"
                @click="checkButton('button' + index)"
              >
                <b-icon icon="check"></b-icon>
              </b-button>
              {{ index + 1 }}
            </b-col>
            <b-col cols="10" class="text-right">{{ ayat }}</b-col>
          </b-row>
        </b-list-group-item>
      </b-list-group>
    </b-container>
  </div>
</template>

<script>
import surahData from "~/assets/juzData.json";

export default {
  data() {
    return {
      memo_value: 0,
      test_value: 0,
      max: null,
      surah: null,
      ayatData: null,
      clicked: false,
      form: { versefrom: null, verseto: null },
      verses: null,
    };
  },
  created() {
    this.surah = {
      name: this.$route.path.substring(16).split("/")[0],
    };
    this.ayatData = this.getAyat();
    this.max = this.ayatData.length;
    this.verses = this.getVersesOptions();
  },
  methods: {
    getAyat() {
      let surah = this.surah;
      var data = [];
      for (let i = 0; i < surahData.data.length; i++) {
        var filterSurah = surahData.data[i].ayahs.filter(function (ayat) {
          return ayat.surah === surah.name;
        });
        data = [...data, ...filterSurah];
      }
      return data.map(function (ayat) {
        return ayat.text;
      });
    },
    getVersesOptions() {
      let verseList = [];
      for (let i = 1; i <= this.ayatData.length; i++) {
        verseList.push(i);
      }
      return verseList;
    },
    checkButton(id) {
      let e = document.getElementById(id);
      if (e.classList.contains("white")) {
        e.classList.remove("white");
        e.classList.add("blue");
        this.memo_value += 1;
      } else {
        e.classList.remove("blue");
        e.classList.add("white");
        this.memo_value -= 1;
      }
    },
    onSubmit(event) {
      event.preventDefault();
      if (this.form.verseto >= this.form.versefrom) {
        for (let i = this.form.versefrom; i <= this.form.verseto; i++) {
          this.checkButton("button" + (i - 1));
        }
      }
      this.$refs["modal-memo"].hide();
    },
  },
};
</script>

<style>
@media (max-width: 424px) {
  .col-10 {
    flex: 0 0 66.666667%;
    max-width: 66.666667%;
  }
}

.memo-surah-title h4 {
  font-weight: 600;
}

.memo-surah-title {
  border-top: 1px solid #ededeb;
}

.ayat-items {
  border-width: 0 0 1px 0;
}

.mark-button,
.btn-secondary:not(:disabled):not(.disabled):active,
.btn-secondary:not(:disabled):not(.disabled).active,
.show > .btn-secondary.dropdown-toggle {
  background-color: #5ec699;
  color: #ffffff;
  border: 0;
  border-radius: 15px;
}

.dropdown-menu {
  max-height: 80vh;
  overflow-y: auto;
}

.check-verse-button {
  width: 20px;
  height: 20px;
  padding: 6px 0;
  border-radius: 15px;
  text-align: center;
  font-size: 10px;
  line-height: 1.42857;
}

.check-verse-button.white {
  background-color: #ffffff;
  color: #aaaaaa;
  border-color: #aaaaaa;
}

.check-verse-button.blue {
  background-color: #49c0db;
  color: #ffffff;
  border-color: #ffffff;
}

.memorize-button {
  background-color: #5ec699;
  border-radius: 15px;
  border: 0;
}
</style>
