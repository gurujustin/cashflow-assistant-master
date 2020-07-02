<template>
    <application-layout>
        <h1 slot="header">Nueva Cuenta Por Cobrar</h1>
        
        <form @submit.prevent="add">
            <div class="form-group">
                <label>Cliente:</label>
                <v-select
                :debounce="1000"
                :on-search="getOptions"
                :on-change="chooseClient"
                :options="options"
                placeholder="Busca un cliente por su nombre..."
                label="name"
                required
                >
            </v-select>
        </div>
        <div class="row">
            <div class="col-md-6">
                <div class="form-group">
                    <label>Fecha:</label>
                    <date-picker class="form-control" v-model="payment.date" :config="datePickerConfig"></date-picker>
                </div>
            </div>
            <div class="col-md-6">
                <label>Total:</label>
                <input type="text" class="form-control" v-model="payment.total" required>
            </div>
        </div>
        <div class="form-group">
            <label>Descripción</label>
            <textarea rows="3" class="form-control" v-model="payment.description" required></textarea>
        </div>
        
        <div class="row">
            <div class="col-md-12">
                <div class="form-group pull-right">
                    <label class="mr-1">Estado?</label>
                    <v-toggle 
                    data-on="Pagado" 
                    data-off="Pendiente" 
                    data-on-style="success" 
                    data-off-style="danger"
                    :data-checked="payment.payed"
                    @changed="togglePayed"
                    >
                </v-toggle>
            </div>
        </div>
    </div>
    
    <div class="form-group">
        <button class="btn btn-primary">Agregar Cuenta <i class="fa fa-plus"></i></button>
    </div>
</form>
</application-layout>
</template>

<script>
    export default {
        data() {
            return {
                options: [],
                payment: {
                    payed: false
                },
                datePickerConfig: {
                    format: 'DD-MM-YYYY',
                    minDate: moment.now()
                }       
            }
        },
        methods: {
            getOptions(search, loading) {
                loading(true)
                axios.get(`/clients?q=${search}`)
                .then(response => {
                    this.options = response.data;
                    loading(false);
                }).catch(error => console.log(error));
            },
            chooseClient(client) {
                this.payment.client_id = client.id;
            },
            togglePayed(value) {
                this.payment.payed = value;
            },
            add() {
                if(! this.payment.client_id) {
                    this.Alert.error('Oops...', 'Debes escoger un cliente para la nueva cuenta por cobrar!');
                    return false;
                }
                axios.post('/payments', this.payment)
                .then(response => {
                    this.Alert.success('Se ha guardado la cuenta por cobrar.')
                    .then(() => {
                        this.$router.push({name: 'payments.index'});
                    })
                })
            }
        }
    }
</script>