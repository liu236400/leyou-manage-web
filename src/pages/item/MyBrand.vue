<template>
    <div>
      <v-layout class="px-3 pb-2">
          <v-flex xs2 >
              <v-btn color="info">新增品牌hello2</v-btn>
          </v-flex>
          <v-spacer/>
          <v-flex xs4>
              <v-text-field label="搜索" hide-details append-icon="search" v-model="key"> </v-text-field>
          </v-flex>
      </v-layout>
      <v-data-table
          :headers="headers"
          :items="brands"
          :pagination.sync="pagination"
          :total-items="totalBrands"
          :loading="loading"
          class="elevation-1"
        >
          <template slot="items" slot-scope="props">
            <td class="text-xs-center">{{ props.item.id }}</td>
            <td class="text-xs-center">{{ props.item.name }}</td>
            <td class="text-xs-center"><img v-if="props.item.image" :src="props.item.image" width="130" height="40"></td>
            <td class="text-xs-center">{{ props.item.letter }}</td>
            <td class="text-xs-center">
              <v-btn flat icon color="info">
                <v-icon>edit</v-icon>
              </v-btn>
              <v-btn flat icon color="error">
                <v-icon>delete</v-icon>
              </v-btn>
            </td>
          </template>
        </v-data-table>
    </div>
</template>

<script>
    export default {
      name: "MyBrand",
      data() {
        return {
          headers: [
            {text: 'id', align: 'center', value: 'id',sortable:true},
            {text: '名称', align: 'center', sortable: false, value: 'name'},
            {text: 'LOGO', align: 'center', sortable: false, value: 'image'},
            {text: '首字母', align: 'center', value: 'letter', sortable: true,},
            {text: '操作', align: 'center', value: 'id', sortable: false}
          ],
          brands:[],
          pagination: {},
          totalBrands: 0,
          loading: false,
          key:"",
        }
      },
      created(){
       this.brands = [
          {id: 1,name: "小米",image:"",letter:"x"},
          {id: 2,name: "小米2",image:"",letter:"x"},
          {id: 3,name: "小米3",image:"",letter:"x"},
        ];
        this.totalBrands = 15;
        //发起ajax请求
        this.loadBrands();
      },
      //监控函数，一旦key发生变化，重新加载函数
      watch:{
        key(){
          this.pagination.page = 1,
          this.loadBrands();
        },
        pagination:{
          deep: true,//复杂类型的深度监控
          handler(){
            this.loadBrands();
          }
        }
      },
      methods: {
        loadBrands(){
          this.loading= true;//显示加载进度条
          this.$http.get("/item/brand/page",{
             params:{
                key: this.key,
                page: this.pagination.page,//当前页
                rows: this.pagination.rowsPerPage,
                sortBy: this.pagination.sortBy,//排序字段
                desc: this.pagination.descending //是否降序
             }
        }).then(resp => {
            this.brands = resp.data.items;
            this.totalBrands = resp.data.total;
            // 完成赋值后，把加载状态赋值为false
            this.loading = false;
          })

        },
      }
    }
</script>

<style scoped>

</style>
