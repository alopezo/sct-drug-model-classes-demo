<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >
      <div class="d-flex align-center">
        <v-img
          alt="SNOMED International Logo"
          class="shrink mr-2"
          contain
          src="https://www.snomed.org/SNOMED/media/SNOMED/other/brand-mark.png"
          transition="scale-transition"
          width="200"
          height="60"
        />
        <h1>Drug Model Classes ECLs</h1>
      </div>
      <v-spacer></v-spacer>
      <v-menu offset-y>
        <template v-slot:activator="{ on, attrs }">
          <v-btn
            color="purple lighten-1"
            dark
            v-bind="attrs"
            v-on="on"
            class="ml-4"
          >
           {{text}}
          </v-btn>
        </template>
        <v-list>
          <v-list-item-group>
            <v-list-item>
              <v-list-item-title @click="setInternational()">
                International Edition
              </v-list-item-title>
            </v-list-item>
            <v-list-item>
              <v-list-item-title @click="setArgentina()">
                Argentina Edition
              </v-list-item-title>
            </v-list-item>
            <v-list-item>
              <v-list-item-title @click="setNorway()">
                Norway Edition
              </v-list-item-title>
            </v-list-item>
          </v-list-item-group>
        </v-list>
      </v-menu>
      <v-btn color="purple lighten-1" class="ml-4">
          <vue-excel-xlsx
            :data="bindings"
            :columns="columns"
            :file-type="'xlsx'"
            :file-name="'ecl-export'"
            :sheet-name="'sheet1'"
            >
            <v-icon right dark class="mr-4">mdi-cloud-download</v-icon>EXPORT BINDINGS
          </vue-excel-xlsx>
      </v-btn>
      <v-menu offset-y>
        <template v-slot:activator="{ on, attrs }">
          <v-btn
            color="purple lighten-1"
            dark
            v-bind="attrs"
            v-on="on"
            class="ml-4"
          >
            <v-icon dark>
              mdi-information
            </v-icon>
          </v-btn>
        </template>
        <v-list>
          <v-list-item-group>
            <v-list-item>
              <v-list-item-icon>
                <v-icon>mdi-github</v-icon>
              </v-list-item-icon>
              <v-list-item-title @click="goToGithub()">
                Source code on GitHub
              </v-list-item-title>
            </v-list-item>
          </v-list-item-group>
          <v-subheader>Bound to SNOMED CT Release:</v-subheader>
          <v-list-item>
            <v-list-item-title>
              Edition
            </v-list-item-title>
            <v-list-item-subtitle>
              Int. Edition
            </v-list-item-subtitle>
          </v-list-item>
          <v-list-item>
            <v-list-item-title>
              Version
            </v-list-item-title>
            <v-list-item-subtitle>
              {{codeSystemVersionDisplay}}
            </v-list-item-subtitle>
          </v-list-item>
        </v-list>
      </v-menu>
    </v-app-bar>
    <v-main>
      <MainTabs :sections="sections" :snowstormBranch="snowstormBranch"/>
      <v-snackbar
        v-model="snackbar"
        :timeout="timeout"
      >
        {{ text }}

        <template v-slot:action="{ attrs }">
          <v-btn
            color="blue"
            text
            v-bind="attrs"
            @click="snackbar = false"
          >
            Close
          </v-btn>
        </template>
      </v-snackbar>
    </v-main>
  </v-app>
</template>

<script>
import MainTabs from './components/MainTabs.vue';

import VueExcelXlsx from "vue-excel-xlsx";
import Vue from "vue";

Vue.use(VueExcelXlsx);

export default {
  name: 'App',

  components: {
    MainTabs,
  },
  mounted() {
    this.bindings = [];
    for (const section in this.sections) {
      for (const binding in this.sections[section].bindings) {
        this.bindings.push({ section: this.sections[section].title, title: this.sections[section].bindings[binding].title, ecl: this.sections[section].bindings[binding].ecl.replace(/\s\s+/g, ' ') })
      }
    }
    Vue.prototype.$snowstormBase = 'https://snowstorm.ihtsdotools.org/snowstorm/snomed-ct';
    Vue.prototype.$codeSystemVersion = '2022-01-31';
    Vue.prototype.$snowstormBranch = `MAIN/${this.$codeSystemVersion}`;
    this.codeSystemVersionDisplay = this.$codeSystemVersion;
  },
  methods: {
    goToGithub() {
      window.open('https://github.com/alopezo/coding-demonstrator', '_blank');
    },
    setInternational() {
      Vue.prototype.$snowstormBase = 'https://snowstorm.ihtsdotools.org/snowstorm/snomed-ct';
      Vue.prototype.$codeSystemVersion = '2022-01-31';
      Vue.prototype.$snowstormBranch = `MAIN/${this.$codeSystemVersion}`;
      Vue.prototype.$langCode = 'en';
      this.snowstormBranch = this.$snowstormBranch;
      this.codeSystemVersionDisplay = this.$codeSystemVersion;
      console.log('setInternational')
      this.text = 'International Edition';
      this.snackbar = true;
    },
    setArgentina() {
      Vue.prototype.$snowstormBase = 'https://snowstorm.ihtsdotools.org/snowstorm/snomed-ct';
      Vue.prototype.$codeSystemVersion = '2021-11-30';
      Vue.prototype.$snowstormBranch = `MAIN/SNOMEDCT-ES/SNOMEDCT-AR/${this.$codeSystemVersion}`;
      Vue.prototype.$langCode = 'es';
      this.snowstormBranch = this.$snowstormBranch;
      this.codeSystemVersionDisplay = this.$codeSystemVersion;
      console.log('setArgentina')
      this.text = 'Argentina Edition';
      this.snackbar = true;
    },
    setNorway() {
      Vue.prototype.$snowstormBase = 'https://snowstorm.ihtsdotools.org/snowstorm/snomed-ct';
      Vue.prototype.$codeSystemVersion = '2021-10-15';
      Vue.prototype.$snowstormBranch = `MAIN/SNOMEDCT-NO/${this.$codeSystemVersion}`;
      Vue.prototype.$langCode = 'no';
      this.snowstormBranch = this.$snowstormBranch;
      this.codeSystemVersionDisplay = this.$codeSystemVersion;
      console.log('setNorway')
      this.text = 'Norway Edition';
      this.snackbar = true;
    }
  },
  data: () => ({
    snowstormBranch: '',
    snackbar: false,
    text: 'International Edition',
    timeout: 2000,
    bindings: [],
    columns : [
                    {
                        label: "Section",
                        field: "section",
                    },
                    {
                        label: "Title",
                        field: "title",
                    },
                    {
                        label: "ECL",
                        field: "ecl",
                    }
                ],
    codeSystemVersionDisplay: '',
    sections: {
        'DM-GRS': {
          title: 'Groupers',
          note: 'This section contains all the "Containing" Medicinal Product classes, plus groupers.',
          bindings: {
            'DM-MPS-MedicinalProduct' : {
              title: 'Medicinal product + groupers',
              type: 'autocomplete',
              ecl: `<< 763158003 |Medicinal product| :
                    [0..0] 1142139005 |Count of base of active ingredient| = *,
                    [0..0] 411116001 |Has manufactured dose form| = *,
                    [0..0] 774158006 |Has product name| = *`,
              value: '',
              note: 'Medicinal products, contain at least provided ingredientes (open world assuption), no information on form.'
            },
            'DM-MPS-MedicinalProductForm' : {
              title: 'Medicinal product form + groupers',
              type: 'autocomplete',
              ecl: `<< 763158003 |Medicinal product| :
                    [0..0] 1142139005 |Count of base of active ingredient| = *,
                    411116001 |Has manufactured dose form| = *,
                    [0..0] 774158006 |Has product name| = *`,
              value: '',
              note: 'Medicinal products, with form details.'
            }
          }
        },
        'DM-MPS': {
          title: 'Medicinal products',
          note: 'This section contains all the "Only" Medicinal product and Medicinal product form classes.',
          bindings: {
            'DM-MPS-MedicinalProductOnly' : {
              title: 'Medicinal product only',
              type: 'autocomplete',
              ecl: `<< 763158003 |Medicinal product| :
                    1142139005 |Count of base of active ingredient| = *,
                    [0..0] 411116001 |Has manufactured dose form| = *,
                    [0..0] 774158006 |Has product name| = *`,
              value: '',
              note: 'Medicinal products, containing only the provided ingredientes (close world assuption), with no information on form.'
            },
            'DM-MPS-MedicinalProductFormOnly' : {
              title: 'Medicinal product form only',
              type: 'autocomplete',
              ecl: `<< 763158003 |Medicinal product| :
                    1142139005 |Count of base of active ingredient| = *,
                    411116001 |Has manufactured dose form| = *,
                    [0..0] 732943007 |Has basis of strength substance (attribute)| = *,
                    [0..0] 774158006 |Has product name| = *`,
              value: '',
              note: 'Medicinal products, with form details, containing only the provided ingredientes (close world assuption).'
            },
            'DM-MPS-MedicinalProductPreciseOnly' : {
              title: 'Medicinal product precisely',
              type: 'autocomplete',
              ecl: `<< 763158003 |Medicinal product| :
                    762949000 |Has precise active ingredient (attribute)| = * ,
                    1142139005 |Count of base of active ingredient| = *,
                    [0..0] 732943007 |Has basis of strength substance (attribute)| = *,
                    [0..0] 774158006 |Has product name| = *`,
              value: '',
              note: `Medicinal products, with form details, precise active ingredient, and containing only the provided ingredientes (close world assuption).
                    <br><b>Not populated in the International Edition</b>`
            },
            'DM-MPS-RealMedicinalProduct' : {
              title: 'Real medicinal product',
              type: 'autocomplete',
              ecl: `<< 763158003 |Medicinal product| :
                    [0..0] 411116001 |Has manufactured dose form| = *,
                    774158006 |Has product name| = *`,
              value: '',
              note: `Real Medicinal products, contain only the provided ingredients (close world assuption), no information on form, and brand name.
                    <br><b>Not populated in the International Edition</b>`
            }
          }
        },
        'DM-CDS': {
          title: 'Clinical Drugs',
          note: 'This section contains all the Clinical Drug classes, abstract and real.',
          bindings: {
            'DM-CDS-ClinicalDrugs' : {
              title: 'Clinical drug',
              type: 'autocomplete',
              ecl: `<< 763158003 |Medicinal product| :
                    732943007 |Has basis of strength substance (attribute)| = *,
                    [0..0] 774158006 |Has product name| = *`,
              value: '',
              note: `Clinical Drugs, with ingredients, form, and strength, but no brand.`
            },
            'DM-CDS-RealClinicalDrugs' : {
              title: 'Real Clinical drug',
              type: 'autocomplete',
              ecl: `<< 763158003 |Medicinal product| :
                    732943007 |Has basis of strength substance (attribute)| = *,
                    774158006 |Has product name| = *`,
              value: '',
              note: `Clinical Drugs, with ingredients, form, strength, and brand.
                    <br><b>Not populated in the International Edition</b>`
            }
          }
        },
        'DM-PCDS': {
          title: 'Packaged Clinical Drugs',
          note: 'This section contains all the packaged Clinical Drug classes, abstract and real.',
          bindings: {
            'DM-CDS-PackagedClinicalDrugs' : {
              title: 'Packaged Clinical drugs',
              type: 'autocomplete',
              ecl: `<< 781405001 |Medicinal product package|: 
                    [0..0] 774158006 |Has product name| = *`,
              value: '',
              note: `Packaged Clinical Drugs, with ingredients, form, strength and package details, but no brand.
                    <br><b>Not populated in the International Edition</b>`
            },
            'DM-CDS-RealPackagedClinicalDrugs' : {
              title: 'Real Packaged Clinical drugs',
              type: 'autocomplete',
              ecl: `<< 781405001 |Medicinal product package|: 
                    774158006 |Has product name| = *`,
              value: '',
              note: `Packaged Clinical Drugs, with ingredients, form, strength, package details, and brand.
                    <br><b>Not populated in the International Edition</b>`
            }
          }
        }
      }
  }),
};
</script>