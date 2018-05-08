<template>
    <list-page :listTitslots='listTitslots' :pagLisNames="pagLisNames" :addListData="addListData" :editListData="editListData" :getDataUrl="getDataUrl" :formData="formData"></list-page>
</template>

<script>
    import listPage from '../common/listPage.vue';
    export default {
        components: {
            listPage
        },
        data (){
            return{
                listTitslots:'事件管理',
                pagLisNames:[
                    {
                        value1: "id",
                        value2:"属性ID"
                    },
                    {
                        value1: "name",
                        value2:"名称"
                    },
                    {
                        value1: "default",
                        value2:"默认值"
                    },
                    {
                        value1: "status",
                        value2:"状态"
                    },
                    {
                        value1: "sort",
                        value2:"排序"
                    }
                ] ,
                addListData:[
                    {
                        value1: "名称",
                        value2:"name"
                    },
                    {
                        value1: "默认值",
                        value2:"default"
                    },
                    {
                        value1: "排序",
                        value2:"sort"
                    }
                ] ,
                editListData:[
                    {
                        value1: "ID",
                        value2:"id"
                    },
                    {
                        value1: "名称",
                        value2:"name"
                    },
                    {
                        value1: "默认值",
                        value2:"default"
                    },
                    {
                        value1: "排序",
                        value2:"sort"
                    }
                ] ,
                formData: {
                    id: '3',
                    name: '4',
                    status: '1',
                    sort:'',
                    default:'6'
                },
                getDataUrl:{
                    listurl: 'Attribute/index',
                    addurl: 'Attribute/create' ,
                    delurl: 'Attribute/destroy',
                    ediurl: 'Attribute/update'
                },        
            } 
        }
    }
</script>

<style scoped>
   
    
</style>