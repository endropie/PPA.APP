<template>
  <q-page padding class="page-index" >
    <q-pull-to-refresh @refresh="TABLE.refresh" inline>
      <q-table  ref="table" class="table-index th-uppercase" color="primary" :dark="LAYOUT.isDark"
        :title="TABLE.getTitle()"
        :data="TABLE.rowData"
        :columns="TABLE.columns"
        :filter="TABLE.filter"
        selection="none"
        :selected.sync="TABLE.selected"
        row-key="id"
        :pagination.sync="TABLE.pagination"
        @request="TABLE.compute"
        :loading="TABLE.loading">

        <template v-slot:top>
          <table-header
            :title="TABLE.getTitle()"
            :TABLE.sync="TABLE"
            :menus="[
              { shortcut: true,
                label: $tc('form.add'),
                detail: $tc('messages.form_new'),
                icon: 'add',
                hidden:!$app.can('customers-create'),
                to: `${TABLE.resource.uri}/create`
              }
            ]">

            <div class="row q-col-gutter-xs" >
              <q-select class="col-12" autocomplete="off"
                multiple use-chips use-input new-value-mode="add"
                dense hide-dropdown-icon
                v-model="FILTERABLE.search" emit-value
                :placeholder="`${$tc('form.search',2)}...`"
                standout="bg-blue-grey-5 text-white"
                :bg-color="LAYOUT.isDark ? 'blue-grey-9' : 'blue-grey-1'"
                :dark="LAYOUT.isDark"
                @input="FILTERABLE.submit">

                <template slot="append">
                  <q-btn flat dense icon="search" dark-percentage color="fadded" @click="FILTERABLE.submit"/>
                </template>
              </q-select>
            </div>

          </table-header>
        </template>

        <!-- slot name syntax: body-cell-<column_name> -->
        <q-td slot="body-cell-prefix" slot-scope="rs" :props="rs" style="width:35px">
          <q-btn v-if="isCanUpdate" dense flat color="light" icon="edit"   :to="`${TABLE.resource.uri}/${rs.row.id}/edit`" />
          <q-btn v-if="isCanDelete" dense flat color="light" icon="delete" @click.native="TABLE.delete(rs.row)" />

          <!-- Resource show -->
          <!-- <q-btn :to="`${TABLE.resource.uri}/${rs.row.id}`"  flat round color="light" size="sm" icon="menu" /> -->
        </q-td>

        <q-td slot="body-cell-order_mode" slot-scope="rs" :props="rs">
          <q-badge :label="$tc(`customers.order_${rs.row.order_mode}`)" class="text-uppercase text-caption" color="lime-10"/>
        </q-td>

        <!-- <q-td slot="body-cell-enable" slot-scope="rs" :props="rs">
          <q-icon :name=" rs.row.enable ? `check`: `times`"></q-icon>
        </q-td> -->

      </q-table>
    </q-pull-to-refresh>
  </q-page>
</template>

<script>
import MixIndex from '@/mixins/mix-index.vue'

export default {
  mixins: [MixIndex],
  data () {
    return {
      TABLE: {
        mode: 'index',
        resource:{
          api: '/api/v1/incomes/customers',
          uri: '/admin/common/customers',
        },
        columns: [
          { name: 'prefix', label: '', align: 'left'},

          { name: 'code', field: 'code', label: 'Intern code', align: 'left', sortable: true },
          { name: 'name', field: 'name', label: 'Name', align: 'left', sortable: true},
          { name: 'order_mode', field: 'order_mode', label: this.$tc('label.mode', 1 ,{v:'Order'}), align: 'left', sortable: true},
          { name: 'phone', label: 'Phone', field: 'phone', align: 'left'},
          { name: 'email', label: 'Email', field: 'email', align: 'left', sortable: true },
          // { name: 'fax', label: 'Fax', field: 'fax', align: 'left', sortable: true },
          // { name: 'enable', label: 'Enable', field: 'enable', align: 'left', sortable: true },
        ],
        rowData:[],
        resData:[],
        pagination : {
          page: 1,
          rowsPerPage: 10,
          rowsNumber: 10 // specifying this determines pagination is server-side,
        },
        filter: '',
        selected: [
          // initial selection => { id: 5 }
        ],
        loading: false,
      },
    }
  },
  created() {
    this.INDEX.load()
  },
  computed:{

    isCanUpdate(){
      return this.$app.can('customers-update')
    },
    isCanDelete(){
      return this.$app.can('customers-delete')
    },
  }
}
</script>