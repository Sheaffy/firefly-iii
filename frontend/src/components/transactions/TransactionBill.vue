<!--
  - TransactionBill.vue
  - Copyright (c) 2021 james@firefly-iii.org
  -
  - This file is part of Firefly III (https://github.com/firefly-iii).
  -
  - This program is free software: you can redistribute it and/or modify
  - it under the terms of the GNU Affero General Public License as
  - published by the Free Software Foundation, either version 3 of the
  - License, or (at your option) any later version.
  -
  - This program is distributed in the hope that it will be useful,
  - but WITHOUT ANY WARRANTY; without even the implied warranty of
  - MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
  - GNU Affero General Public License for more details.
  -
  - You should have received a copy of the GNU Affero General Public License
  - along with this program.  If not, see <https://www.gnu.org/licenses/>.
  -->

<template>
  <div class="form-group">
    <div class="text-xs d-none d-lg-block d-xl-block">
      {{ $t('firefly.bill') }}
    </div>
    <div class="input-group">
      <select
          ref="bill"
          :title="$t('firefly.bill')"
          v-model="value"
          autocomplete="off"
          class="form-control"
          name="bill_id[]"
          v-on:submit.prevent
      >
        <option v-for="bill in this.billList" :value="bill.id" :label="bill.name">{{ bill.name }}</option>

      </select>
    </div>
  </div>
</template>

<script>

import {createNamespacedHelpers} from "vuex";

const {mapState, mapGetters, mapActions, mapMutations} = createNamespacedHelpers('transactions/create')

export default {
  props: ['value', 'index'],
  name: "TransactionBill",
  data() {
    return {
      billList: []
    }
  },
  created() {
    this.collectData();
  },
  methods: {
    ...mapMutations(
        [
          'updateField',
        ],
    ),
    collectData() {
      this.billList.push(
          {
            id: 0,
            name: this.$t('firefly.no_bill'),
          }
      );
      this.getBills();
    },
    getBills() {
      axios.get('./api/v1/bills')
          .then(response => {
                  this.parseBills(response.data);
                }
          );
    },
    parseBills(data) {
      for (let key in data.data) {
        if (data.data.hasOwnProperty(key) && /^0$|^[1-9]\d*$/.test(key) && key <= 4294967294) {
          let current = data.data[key];
          this.billList.push(
              {
                id: parseInt(current.id),
                name: current.attributes.name
              }
          );
        }
      }
    },
  },
  watch: {
    value: function(value) {
      this.updateField({field: 'bill_id', index: this.index, value: value});
    }
  },
  computed: {
    ...mapGetters(
        [
                    'transactionType',
                    'transactions',
                  ]
    )
  }
}
</script>

