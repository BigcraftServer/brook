<template>
    <v-layout>
        <v-flex xs12 sm12 md6>
            <v-card>
                <v-container fluid>
                    <v-layout column v-if="initSucceed">
                        <v-flex>
                            <v-select
                                :items="ServerNames"
                                v-model.trim="selectedServer"
                                label="Servers"
                                class="input-group--focused"
                                @change="selectVal"
                                ></v-select>
                        </v-flex>
                        <v-flex>
                            <v-text-field
                                label="ServerName"
                                placeholder="LA.CN2"
                                v-model.trim="o.ServerName"
                                ></v-text-field>
                        </v-flex>
                        <v-flex>
                            <v-select
                                :items="['Brook', 'Brook Stream', 'Shadowsocks' ]"
                                v-model.trim="o.Type"
                                label="Type"
                                class="input-group--focused"
                                ></v-select>
                        </v-flex>
                        <v-flex>
                            <v-text-field
                                label="Server"
                                placeholder="1.2.3.4:5"
                                v-model.trim="o.Server"
                                ></v-text-field>
                        </v-flex>
                        <v-flex>
                            <v-text-field
                                label="Password"
                                placeholder="Your server password"
                                v-model="o.Password"
                                :append-icon="passwordVisibility ? 'visibility' : 'visibility_off'"
                                :append-icon-cb="() => (passwordVisibility = !passwordVisibility)"
                                :type="passwordVisibility ? 'text' : 'password'"
                                counter
                                ></v-text-field>
                        </v-flex>
                        <v-flex>
                            <v-btn class="info" @click="save">Add/Save</v-btn>
                            <v-btn class="info" @click="deleteServer">Delete Currently Server</v-btn>
                        </v-flex>
                    </v-layout>
                </v-container>
            </v-card>
        </v-flex>
        <v-snackbar v-model="hey">
            {{girl}}
        </v-snackbar>
    </v-layout>
</template>
<script>
export default {
    data: () => ({
        initSucceed : false,
        o: {
            Type: 'Brook',
            Server: '',
            ServerName: '',
            Password: '',
            TCPTimeout: 60,
            TCPDeadline: 0,
            UDPDeadline: 60,
            UDPSessionTime: 60,
        },
        selectedServer: "",
        servers:{},
        ServerNames:[],
        passwordVisibility: false,
        hey: false,
        girl: "",
    }),

    computed: {
    },

    watch: {
    },

    created () {
        this.initialize()
    },

    methods: {
        deleteServer(){
            delete this.servers[this.selectedServer];
            var context = this;
            this.$nextTick(()=> {
                context.ServerNames = Object.keys(context.servers);
                if(context.ServerNames.length!=0){
                    context.o = context.servers[context.ServerNames[0]];
                    context.selectedServer = context.o.ServerName;
                }else{
                    context.o.ServerName = '';
                    context.o.Type = 'Brook';
                    context.o.Server = '';
                    context.o.Password = '';
                }
            });
        },
        selectVal(){
            var context = this;
            this.$nextTick(()=> {
                context.o = context.servers[context.selectedServer];
            });
        },
        initialize () {
            var s = localStorage.getItem('brook/server');
            if (s){
                this.o = JSON.parse(s);
                this.selectedServer = this.o.ServerName;
            }
            var _servers = localStorage.getItem('brook/servers');
            if (_servers){
                this.servers = JSON.parse(_servers);
            }
            this.ServerNames = Object.keys(this.servers);
            this.initSucceed = true;
        },
        save () {
            if(!/.+?\:\d+/.test(this.o.Server)){
                this.girl = "Invalid Server";
                this.hey = true;
                return;
            }
            localStorage.setItem('brook/server', JSON.stringify(this.o));
            this.girl = "OK";
            this.hey = true;
            if(this.o.ServerName){
                this.servers[this.o.ServerName] = this.o;
                let that = this;
                this.$nextTick(()=>{
                    this.selectedServer = this.o.ServerName
                });
            }
                
            localStorage.setItem('brook/servers', JSON.stringify(this.servers));
        },
    },
}
</script>
