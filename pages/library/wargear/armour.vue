<template xmlns:v-slot="http://www.w3.org/1999/XSL/Transform">

  <div>
    <div class="elevation-4 mb-4 p-0">
      <ul class="v-breadcrumbs theme--light">
        <li class="v-breadcrumbs__item">
          <nuxt-link to="/" class="v-breadcrumbs__item"><img src="/favicon-16x16.png" /></nuxt-link>
        </li>
        <li class="v-breadcrumbs__divider">/</li>
        <li class="v-breadcrumbs__item">
          <nuxt-link to="/library" class="v-breadcrumbs__item">Library</nuxt-link>
        </li>
        <li class="v-breadcrumbs__divider">/</li>
        <li class="v-breadcrumbs__item">
          <nuxt-link to="/library/wargear" class="v-breadcrumbs__item">Wargear</nuxt-link>
        </li>
        <li class="v-breadcrumbs__divider">/</li>
        <li class="v-breadcrumbs__item">
          <nuxt-link to="/library/wargear/armour" class="v-breadcrumbs__item--disabled v-breadcrumbs__item">Armour</nuxt-link>
        </li>
      </ul>
    </div>

    <v-layout justify-center row wrap>

    <v-flex xs12>
      <v-card>
        <v-card-text>
          <v-layout justify-center row wrap>
            <v-flex xs12 sm6>
              <v-text-field
                v-model="searchQuery"
                box
                dense
                clearable
                label="Search"></v-text-field>
            </v-flex>

          </v-layout>
        </v-card-text>
      </v-card>
    </v-flex>

    <v-flex xs12>
      <v-card>
        <v-data-table
          v-bind:headers="headers"
          v-bind:items="armour"
          v-bind:pagination.sync="pagination"
          v-bind:expand="expand"
          v-bind:search="searchQuery"
          disable-initial-sort
          item-key="title"
          hide-actions
        >
          <template v-slot:items="props">
            <tr>
              <td>{{ props.item.name }}</td>
              <td>{{ props.item.subtype }}</td>
              <td class="text-lg-center">
                <span v-if="props.item.meta && props.item.meta.length > 0">{{ props.item.meta[0].armourRating }}</span>
              </td>
              <td>
                <span v-if="props.item.meta && props.item.meta.length > 0 && props.item.meta[0].traits && props.item.meta[0].traits.length >0">{{ props.item.meta[0].traits.join(', ') }}</span>
              </td>
              <td>{{ props.item.value }} {{ props.item.rarity }}</td>
              <td>{{ props.item.keywords.join(', ') }}</td>
            </tr>
          </template>

        </v-data-table>

        <div class="text-xs-center pt-2">
          <v-pagination v-model="pagination.page" :length="pages" />
        </div>
      </v-card>
    </v-flex>

    <v-flex xs12>

      <v-card>
        <v-card-text>
          <h1>Search the Vault for precious, fanmade hombrews</h1>
          <p>
            This is a curated list of homebrews from fans, found in the internet. I credit the author and link to their
            community pages, as good as I could, if I find them either in the document found or on their respective page.
            If want to add, remove or change your homebrew content or if you want to propose changes regarding links,
            you can mail me to <a href="mailto:docsofdoom+vault@gmail.com?subject=Vault Request">docsofdoom+vault(at)gmail.com</a>.
           </p>
        </v-card-text>
      </v-card>

    </v-flex>
  </v-layout>

  </div>

</template>

<script>
export default {
  components: {},
  head() {
    return {
      title: 'Armour - Wrath & Glory Wargear Reference | Library',
      meta: [
        {
          hid: 'description',
          name: 'description',
          content: '',
        },
      ],
    };
  },
  layout: 'library',
  async asyncData({ app }) {
    const response = await app.$axios.get(`/api/wargear/`);
    return {
      wargearRepository: response.data,
    };
  },
  data() {
    return {
      searchQuery: '',
      settingFilter: [],
      contentFilter: [],
      pagination: {
        sortBy: 'title',
        rowsPerPage: -1,
      },
      headers: [
        { text: 'Name', align: 'left', value: 'name', class: '' },
        { text: 'Subtype', align: 'left', value: 'subtype', class: '' },
        { text: 'Armour Rating', align: 'center', value: 'damage', class: '' },
        { text: 'Traits', align: 'left', value: 'traits', class: '' },
        { text: 'Value', align: 'left', value: 'value', class: '' },
        { text: 'Keywords', align: 'left', value: 'keywords', class: '' },
      ],
      expand: false,
    };
  },
  computed: {
    armour() {
      return this.wargearRepository.filter( gear => ['Armour'].includes(gear.type));
    },
    breadcrumbItems() {
      return [
        {
          text: 'D', disabled: false, nuxt: true, exact: true, to: '/',
        },
        {
          text: 'Vault', disabled: false, nuxt: true, exact: true, to: '/vault',
        },
      ];
    },
    searchResults() {
      let filteredResults = this.vaultItems;

      if (this.searchQuery) {
        // filteredResults = filteredResults.filter(h => (h.title.toLowerCase().indexOf(this.searchQuery.toLowerCase()) >= 0))
      }

      if (this.contentFilter.length > 0) {
        filteredResults = filteredResults.filter(h => [...h.topics, ...h.keywords]
          .some(c => this.contentFilter.includes(c)));
      }

      return filteredResults;
    },
    pages() {
      if (this.pagination.rowsPerPage == null
        || this.pagination.totalItems == null
      ) return 0;

      return Math.ceil(this.pagination.totalItems / this.pagination.rowsPerPage);
    },
  },
  methods: {
    changeSort(column) {
      if (this.pagination.sortBy === column) {
        this.pagination.descending = !this.pagination.descending;
      } else {
        this.pagination.sortBy = column;
        this.pagination.descending = false;
      }
    },
  },
};
</script>

<style scoped lang="css">
</style>