<template>
    <div>
        <b-row>
            <b-col xl="4">
                <div class="search-box-container">
                    <b-card title="Search Here" >
                        <hr class="my-4">
                        <form >
                            <b-card-text>
                                <label>Select Main Type</label>
                                <b-form-select
                                        @change="loadFields()"
                                        v-model="main_types"
                                        :options="showMainType"
                                >
                                </b-form-select>
                            </b-card-text>

                            <b-card-text>
                                <label>Select Search Field</label>
                                <b-form-select
                                        @change="selectField()"
                                        v-model="field"
                                        :options="fieldList"
                                >
                                </b-form-select>
                            </b-card-text>

                            <b-card-text>
                                <label>Keyword</label>
                                <b-form-textarea
                                        id="textarea"
                                        v-model="keyword"
                                        placeholder="Enter search keywords..."
                                        rows="3"
                                        max-rows="6"
                                ></b-form-textarea>
                            </b-card-text>

                            <div v-if="main_types && field">
                                <b-button @click="resetMethod()" variant="primary" >Reset</b-button>
                                <b-button @click="searchFormSubmit()" variant="success" >Search</b-button>
                            </div>
                        </form>
                    </b-card>
                </div>
            </b-col>
            <b-col>
                <div class="search-box-container">
                    <b-card title="Search Result" >
                        <div v-if="searchResponse.status || searchResult.length ">
                            <div class="result"
                                 v-if="searchResult.length">
                                <label >Result count {{ searchResult.length }}</label>
                                <b-button
                                        @click="clearSearchResult()"
                                        class="clear_result_btn"
                                        variant="warning">
                                    Clear Search Result
                                </b-button>
                                <div class="clearfix"></div>
                            </div>
                            <div v-else >
                                No Result Found
                            </div>
                        </div>
                        <hr class="my-4">

                        <div role="tablist">
                            <b-card no-body class="mb-1"
                                    v-for="(result, index) in searchResult"
                                    :key="index"
                            >
                                <b-card-header header-tag="header" class="p-1" role="tab">
                                    <b-button block v-b-toggle="'collapse' + index"  variant="info">Id {{result._id.value}}</b-button>
                                </b-card-header>
                                <b-collapse
                                        :id="'collapse' + index"
                                        :visible="index===0"
                                        :accordion="showMainType"
                                        role="tabpanel"
                                >
                                    <b-card-body>
                                        <table class="search_table table table-striped " >
                                            <tbody>
                                                <tr v-for="(val , i2) in result"
                                                    :key="i2"
                                                >
                                                    <th scope="row">{{ val.label }}</th>
                                                    <td>:</td>
                                                    <td class="search_value">{{ val.value }}</td>
                                                </tr>
                                            </tbody>
                                        </table>


                                    </b-card-body>
                                </b-collapse>
                            </b-card>

                        </div>
                    </b-card>
                </div>
            </b-col>
        </b-row>
    </div>
</template>

<script>
    export default {
        props: {
            showMainType: Object,
            api_url: String
        },
        methods:{
            searchFormSubmit() {

                let currentObj = this
                this.axios.post(this.api_url+'/api/v1/search', {
                    main_types: this.main_types,
                    field: this.field,
                    keyword: this.keyword
                }).then(function (response) {
                    console.log(response)
                    currentObj.searchResult = response.data.data;
                    currentObj.searchResponse = response.data;
                }).catch(function (error) {
                    currentObj.searchResult = error;
                });

            },
            clearSearchResult(){
                this.searchResult = {}
                this.searchResponse = {}
            },
            resetMethod(){
                this.main_types = null
                this.fieldList = {}
                this.field = null
                this.keyword = ''
                this.searchResponse = {}
            },
            selectField(){
                this.keyword = ''
            },
            loadFields(){
                //console.log(this.type_selected);
                this.fieldList = {}
                this.field = null
                this.keyword = ''
                fetch(this.api_url+'/api/v1/fields/'+ this.main_types,{
                    method: 'get'
                }).then((response) => {
                    return response.json()
                }).then((jsonData) => {
                    this.fieldList = jsonData.data
                })
            }
        },
        computed: {
           /* createMainTypes(){
                let types = this.showMainType
                //types.push('Select Here');
                console.log(types);
                return types
            }*/
        },
        data(){
            return {
               /* selectMainTypeList: this.createMainTypes(),*/
                main_types: null,
                field: null,
                fieldList: {},
                searchResult:{},
                searchResponse:{},
                keyword: '',
                result_counter: 0
            }
        }
    }
</script>
<style scoped>
    .btn{
        margin: 5px;
    }
    .card-text label{
        float: left;
    }
    .search_table .search_value{
        text-align: left;
    }
    .result label{
        float: left;
    }
    .result .clear_result_btn{
        float: right;
    }
</style>