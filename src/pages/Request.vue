/* eslint-disable @typescript-eslint/restrict-template-expressions */ /*
eslint-disable @typescript-eslint/no-unsafe-member-access */
<template>
  <q-page class="column items-center justify-evenly">
    <!--
      Show on failue
    -->
    <div
      style="text-align: center; font-size: 32px"
      v-if="this.unsuccessfullRequest"
    >
      <q-icon name="sentiment_dissatisfied" />
      <p style="font-family: 'Ubuntu', sans-serif">
        Something went wrong. Please try again.
      </p>
      <q-btn
        color="purple"
        label="Try again"
        @click="this.unsuccessfullRequest = false"
      />
    </div>

    <!--
      Show on No results
    -->
    <div style="text-align: center; font-size: 32px" v-if="this.noResultsFound">
      <q-icon name="sentiment_dissatisfied" />
      <p style="font-family: 'Ubuntu', sans-serif">
        No spaces found at this time. Try again later.
      </p>
    </div>

    <!--
      Show on fetched results
    -->
    <div v-if="resultsFound" class="q-pa-md results-wrapper">
      <q-card
        v-for="(result, index) in results"
        :key="index"
        class="results-card"
      >
        <q-card-section class="column results-card--info">
          <div class="column results-header">
            <div class="text-h6 results-title">
              {{ result.region }} -- {{ result.type }}
            </div>
            <div class="text-subtitle2 results-subtitle">
              Available from
              <span class="accent">{{ result.availability_start }}</span> to
              <span class="accent">{{ result.availability_end }}</span>
            </div>
          </div>
          <div class="row">
            <div class="col-6">
              <div class="results-info">
                <span class="results-info--key">Pets Allowed</span>:
                <span class="results-info--value">
                  {{ result.pet_friendly ? 'Yes' : 'No' }}
                </span>
              </div>
            </div>
            <div class="col-6">
              <div class="results-info">
                <span class="results-info--key">Maximum Guests</span>:
                <span class="results-info--value">
                  {{ result.visitors_max }}
                </span>
              </div>
            </div>
          </div>
        </q-card-section>

        <q-separator />

        <q-card-actions align="right" class="results-card--actions">
          <q-btn flat color="primary" @click="onShowContactDetails(result)">
            Contact
          </q-btn>
        </q-card-actions>
      </q-card>
    </div>

    <q-dialog v-model="contactInfoVisible">
      <q-card>
        <q-card-section>
          <div class="text-h6">Property Contact Details</div>
        </q-card-section>

        <q-card-section class="q-pt-none column">
          <div>
            <span>Owner</span>:
            <span class="text-bold">
              {{ selectedResult.owner }}
            </span>
          </div>
          <div>
            <span>Email</span>:
            <span class="text-bold">
              {{ selectedResult.email }}
            </span>
          </div>
          <div>
            <span>Phone Number</span>:
            <span class="text-bold">
              {{ selectedResult.telephone }}
            </span>
          </div>
        </q-card-section>

        <q-card-actions align="right">
          <q-btn flat label="OK" color="primary" v-close-popup />
        </q-card-actions>
      </q-card>
    </q-dialog>

    <!-- Main Page On Load -->

    <h2
      v-if="!this.unsuccessfullRequest && !this.resultsFound"
      class="text-center"
    >
      If you need a space, fill the form.<br /><br />
      💌
    </h2>
    <div
      v-if="!this.unsuccessfullRequest && !this.resultsFound"
      class="q-pa-md"
      style="max-width: 400px"
    >
      <q-form @submit="onSubmit" @reset="onReset" class="q-gutter-md">
        <q-select
          standout
          v-model="spacetype"
          :options="space_options"
          label="Space Type"
          hint="The type of space you're requesting."
        />

        <q-select
          standout
          v-model="region"
          :options="region_options"
          label="Region"
          hint="The region you're looking for a space."
        />

        <q-select
          standout
          v-model="pet"
          :options="pet_options"
          label="Pet Friendly"
          hint="If you require the space to be pet friendly."
        />

        <q-badge color="secondary">
          Sleeps (visitors): {{ visitorscount }}
        </q-badge>
        <q-slider
          v-model="visitorscount"
          color="purple"
          markers
          snap
          :min="1"
          :max="15"
        />

        <div class="q-pa-md">
          <q-date
            subtitle="Availability"
            v-model="timeperiod"
            range
            color="purple"
          />
        </div>

        <q-toggle v-model="accept" label="I accept the conditions" />

        <div>
          <q-btn label="Find" type="submit" color="purple" />
          <q-btn
            label="Reset"
            type="reset"
            color="purple"
            flat
            class="q-ml-sm"
          />
        </div>
      </q-form>
    </div>
  </q-page>
</template>

<script>
import { useQuasar } from 'quasar';
import { ref } from 'vue';
import { api } from 'boot/axios';

export default {
  setup() {
    const $q = useQuasar();

    const resultsFound = ref(false);
    const noResultsFound = ref(false);
    const unsuccessfullRequest = ref(false);
    const spacetype = ref(null);
    const region = ref(null);
    const pet = ref(null);
    const visitorscount = ref(1);
    const timeperiod = ref({ from: '2020/07/08', to: '2020/07/17' });
    const accept = ref(false);
    const results = ref([]);
    const contactInfoVisible = ref(false);
    const selectedResult = ref({});

    return {
      results, // for results
      contactInfoVisible,
      selectedResult,
      resultsFound,
      noResultsFound,
      unsuccessfullRequest,
      timeperiod,
      pet,
      pet_options: ['Yes', 'No'],
      visitorscount,
      region,
      region_options: [
        'Achaea',
        'Acarnania',
        'Arcadia',
        'Argolis',
        'Arta',
        'Athens',
        'Boeotia',
        'Chalkidiki',
        'Chania',
        'Chios',
        'Corfu',
        'Corinthia',
        'Cyclades',
        'Dodecanese',
        'Drama',
        'East Attica',
        'Elis',
        'Euboea',
        'Evros',
        'Evrytania',
        'Florina',
        'Heraklion',
        'Grevena',
        'Imathia',
        'Ioannina',
        'Kastoria',
        'Kavala',
        'Kefallinia',
        'Karditsa',
        'Kilkis',
        'Kozani',
        'Laconia',
        'Larissa',
        'Lasithi',
        'Lefkada',
        'Lesbos',
        'Magnesia',
        'Messenia',
        'Pella',
        'Phocis',
        'Phthiotis',
        'Pieria',
        'Piraeus',
        'Preveza',
        'Rethymno',
        'Rhodope',
        'Samos',
        'Serres',
        'Thesprotia',
        'Thessaloniki',
        'Trikala',
        'West Attica',
        'Xanthi',
        'Zakynthos',
      ],
      spacetype,
      space_options: [
        'Flat',
        'Condo',
        'Single-Family',
        'Cottage',
        'Villa',
        'Tiny Home',
        'Shared Space',
      ],
      accept,

      onSubmit() {
        if (accept.value !== true) {
          $q.notify({
            color: 'red-5',
            textColor: 'white',
            icon: 'warning',
            message: 'You need to accept the license and terms first',
          });
        } else {
          // Make request to offer
          /* eslint-disable */
          const spaceRequest = {
            type: spacetype.value,
            region: region.value,
            availability_start: timeperiod.value['from'],
            availability_end: timeperiod.value['to'],
            visitors_max: visitorscount.value,
            pet_friendly: pet.value,
          };

          api.post('/api/v1/space/request', spaceRequest).then((response) => {
            if (response.status == 200) {
              if (response.data['status'] == 'no_results') {
                noResultsFound.value = true;
              } else {
                resultsFound.value = true;
                results.value = response.data;
              }
            } else {
              unsuccessfullRequest.value = true;
            }
          });
        }
      },

      onReset() {
        spacetype.value = null;
        region.value = null;
        pet.value = null;
        visitorscount.value = null;
        timeperiod.value = null;

        accept.value = false;
      },

      onShowContactDetails(propertyAttributes) {
        contactInfoVisible.value = true;
        selectedResult.value = propertyAttributes;
      },
    };
  },
};
</script>

<style scoped lang="scss">
.accent {
  font-style: italic;
  font-weight: bold;
  padding: 0 3px;
}

.results-wrapper {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  grid-template-rows: 1fr 1fr 1fr;
  grid-gap: 60px;

  .results-card {
    width: 400px;

    .results-card--info {
      background: #fdeafa;

      .results-header {
        .results-title {
          font-style: italic;
        }
        .results-subtitle {
        }
      }
      .results-info {
        padding-top: 15px;

        .results-info--key {
          text-decoration: underline;
        }
        .results-info--value {
          font-weight: bold;
          padding-left: 5px;
        }
      }
    }

    .results-card--actions {
    }
  }
}
</style>
