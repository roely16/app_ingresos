<template>

	<div>
		<div>
			<b-navbar toggleable="lg" type="dark" variant="info">
				<b-navbar-brand href="#">Ingresos IUSI</b-navbar-brand>

				<b-navbar-nav class="ml-auto">
					<b-nav-item right href="#" v-on:click="reload" :disabled="loading">
					<font-awesome-icon icon="sync-alt" size="lg" />
					</b-nav-item>
				</b-navbar-nav>

			</b-navbar>
		</div>

		<br>
		<b-container class="bv-example-row">
			<b-row v-if="!loading && !error_connect">
				<b-col>
					<div>
						<b-card title="Meta 500 Mill.">
							
							<br>

							<vc-donut
							background="white" foreground="grey"
							unit="px" :thickness="20"
							legendPlacement="top"
							:sections="sections" :total="100"
							>
							<h1>{{ porcentaje_total }}%</h1>
							<h2>{{ valor_total }} Mill.</h2>
							</vc-donut>

							<br>

							<div v-if="showDetail">
							<b-table striped hover :items="items" :fields="fields">
								<template slot="HEAD[value]" slot-scope="data">
								<div class="text-right">
									<span>{{ data.label }}</span>
								</div>
								</template>
								<template slot="[value]" slot-scope="data">
								<div class="text-right">
									<span>{{ data.item.value }}</span>
								</div>
								</template>
							</b-table>
							</div>
							
							<b-row align-h="end">
							<b-col class="text-right">
								<a href="#" v-on:click="mostrarDetalle" class="card-link text-right">{{ !showDetail ? "Mostrar Detalles" : "Ocultar" }}</a>
							</b-col>
							</b-row>
						
						</b-card>
					</div>
				</b-col>
			</b-row>
			
			
			<b-row v-if="loading">
				<b-col>
					<div class="d-flex justify-content-center mb-3">
					<b-spinner style="width: 3rem; height: 3rem;" variant="primary" label="Large Spinner"></b-spinner>
					</div>
				</b-col>
			</b-row>

			<b-row v-if="error_connect" align-v="center">
				<b-col cols="12" class="text-center">
					<h3 class="text-center mb-4">Existe un problema con su conexión a internet.</h3>
					<b-button class="text-center" variant="danger" size="lg" v-on:click="reload">Reintentar 
						<font-awesome-icon icon="sync-alt" />
					</b-button>
				</b-col>
			</b-row>
		</b-container>
    
  	</div>
  


</template>

<style>
	.md-app {
		max-height: 700px;
		border: 1px solid rgba(#000, .12);
	}
  
</style>

<script>

	import axios from 'axios'

	export default {

		name: 'App',
		data: () => ({
			menuVisible: false,
			loading: false,
			sections: [
				{ label: 'Green section', value: 80, color: 'green' },
			],
			showDetail: false,
			porcentaje_total: '',
			valor_total: '',
			loading: false,
			error_connect: false,
			detalle_total: [], 
			items: [],
			fields: [
				{
					key: 'name',
					label: 'Descripción'
				},
				{
					key: 'value',
					label: 'Cantidad (en millones)'
				},
			],
		}),
		methods: {
			reload: function(){
				this.getData()
			}, 
			getData: function(){

				this.loading = true
				this.error_connect = false

				let data = {
                    "name": "consultar_ingresos",
                    "param": {}
				}
				
				axios({
					method: 'POST',
                    url: 'https://udicat.muniguate.com/apps/api_ingresos_app_udi/',
                    headers: {
                        'Content-Type': 'application/json'
					},
					data: data
				})
				.then(data => {

					console.log(data)

                    this.loading = false

                    this.sections = [data.data.response.result.GRAFICA_TOTAL];
                    this.porcentaje_total = data.data.response.result.TOTALES.PORCENTAJE
                    this.valor_total = data.data.response.result.TOTALES.TOTAL
                    this.items = data.data.response.result.DETALLE_TOTAL

				})
				.catch(error => {
					this.loading = false
					this.error_connect = true
				});
			},
			mostrarDetalle: function(){

                this.showDetail = !this.showDetail

			},
		}, 
		mounted(){
			this.getData()
		}
	}
</script>

