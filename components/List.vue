<template>
    <div>
        <v-card max-width="475" class="mx-auto">
            <v-list subheader two-line flat>
                <!-- <v-subheader>買うものリスト</v-subheader> -->
                <!-- <v-list-item-group　v-model="settings"　multiple> -->
                    <div v-for="(item,key) in itemList" :key="key" >
                        <v-list-item>
                                <v-list-item-action>
                                <div class="flexContainer">
                                    <!-- <v-checkbox v-model="ex4" label="orange" color="orange" value="orange" hide-details></v-checkbox> -->
                                    <v-checkbox @change="checkItem(item)" v-model="item.status" color="orange darken-3" hide-detailsÏ></v-checkbox>
                                </div>
                                </v-list-item-action>
                                <v-list-item-content>
                                <v-list-item-title>{{item.name}}</v-list-item-title>
                                </v-list-item-content>
                        </v-list-item>
                    </div>
                <!-- </v-list-item-group> -->
            </v-list>
        </v-card>
    </div>
</template>

<script>
import { API } from 'aws-amplify'
import { updateTodo } from '~/src/graphql/mutations'
export default {
  data() {
    return {
        // itemList:[
        //     {name:"aaa"},
        //     {name:"bbb"},
        //     {name:"ccc"}
        // ]      
    }
  },
  props:{
      itemList:{
          type:Array,
          default:[],
          required:true
      }
  },
  methods:{
    async checkItem(item){
      const id = item.id;
      const status = item.status;
      const now = new Date();
      let ttl;
      if(status){
        ttl = Math.floor((now.setDate(now.getDate() + 1)) / 1000);  
      }else{
        ttl = undefined;
      }
      const param = {id,status,ttl}
      await API.graphql({
        query: updateTodo,
        variables: {input:param},
      })
    },
  }
}
</script>

<style>

</style>