<template>
    <v-autocomplete
        v-model="binding.value"
        :label="binding.title"
        :items="items"
        :loading="loading"
        :search-input.sync="search"
        no-data-text="Type at least 3 characters to search..."
        cache-items
        clearable
        :multiple="binding.isMultiple == true"
    ></v-autocomplete>
</template>
<script>
  import axios from "axios";

  export default {
    data: () => ({
        search: null,
        loading: false,
        items: []
    }),
    props: {
      binding: {
        type: Object
      }
    },
    watch: {
      search (val) {
        if (this.timeout) 
          clearTimeout(this.timeout); 

        this.timeout = setTimeout(() => {
          val && val != this.binding.value && val.length > 2 && this.querySelections(val)
        }, 300); // delay
      },
    },
    methods: {
      querySelections (v) {
        this.loading = true;
        var base = this.binding.base || this.$snowstormBase
        var branch = this.binding.branch || this.$snowstormBranch
        var langCode = this.$langCode || 'en';
        var queryString = `${base}/${branch}/concepts?activeFilter=true&term=${v}&
                            termActive=true&language=${langCode}&offset=0&limit=50&ecl=${encodeURIComponent(this.binding.ecl)}`;
        axios
          .get(queryString, 
              {
                  headers: {
                      'Accept-Language': langCode
                  }
              })
          .then(response => {
            this.items = response.data.items.map( e => e.fsn.term );
            this.loading = false;
          })
      }
    }
  }
</script>
