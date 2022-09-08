<template>
	<div>
		<h1 class="text-center">Agenda de Contactos</h1>
		<hr>
		<div>
			<div class="row">
				<div class="col-md-6">
					<button @click="update = false;openModal();" type="button" class="btn btn-primary">
					  Nuevo Contacto
					</button>
				</div>
				<div class="col-md-6 ">
					<div class="input-group mb-2">
		 				 <input v-model="busqueda" type="text" class="form-control" placeholder="Busqueda ..." >
		  				<button @click = "search()" class="btn btn-secondary" type="button" id="button-addon2">Buscar</button>
					</div>
				</div>
			</div>
		</div>
		<!-- Button trigger modal -->
		

		<!-- Modal -->
		<div class="modal" :class="{ show:modal }">
		  <div class="modal-dialog">
		    <div class="modal-content">
		      <div class="modal-header">
		        <h5 class="modal-title" id="exampleModalLabel">{{ titleModal }}</h5>
		        <button @click="closeModal();" type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
		      </div>
		      <div class="modal-body">
		        <div>
		        	<label for="Nombre">Nombre</label>
		        	<input v-model="contact.nombre" type="text" class="form-control" id="nombre">
		        </div>
		        <div>
		        	<label for="Telefono">Telefono</label>
		        	<input v-model="contact.telefono" type="text" class="form-control" id="telefono">
		        </div>
		        <div>
		        	<label for="fecha nacimiento">Fecha nacimiento</label>
		        	<input v-model="contact.fecha_nacimiento" type="date" class="form-control" id="fecha_nacimiento">
		        </div>
		        <div>
		        	<label for="Direccion">Direccion</label>
		        	<input v-model="contact.direccion" type="text" class="form-control" id="direccion">
		        </div>
		        <div>
		        	<label for="Correo Electronico">Correo Electronico</label>
		        	<input v-model="contact.correo" type="text" class="form-control" id="correo">
		        </div>
		      </div>
		      <div class="modal-footer">
		        <button @click="closeModal();" type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cerrar</button>
		        <button @click="save();" type="button" class="btn btn-success">Guardar Cambios</button>
		      </div>
		    </div>
		  </div>
		</div>
		<table class="table  table-hover">
		  <thead>
		    <tr class="table-dark">
		      <th scope="col">Nombre</th>
		      <th scope="col">Telefono</th>
		      <th scope="col">Fecha Nacimiento</th>
		      <th scope="col">Direcciòn</th>
		      <th scope="col">Correo Electronico</th>
		      <th scope="col" colspan="2" class="text-center">Acciòn</th>
		    </tr>
		  </thead>
		  <tbody>
		    <tr v-for="contact in contacts" :key="contact.id">
		      <th >{{ contact.nombre }}</th>
		      <td>{{ contact.telefono }}</td>
		      <td>{{ contact.fecha_nacimiento }}</td>
		      <td>{{ contact.direccion }}</td>
		      <td>{{ contact.correo }}</td>
		      <td>
		      	<button @click="update = true;openModal(contact);" class="btn btn-warning">Editar</button>
		      	<button @click = "eliminar(contact.id)" class="btn btn-danger">Eliminar</button>
		      </td>
		    </tr>		    
		  </tbody>
		</table>
	</div>
</template>

<script>
	export default {
		data () {
			return {
				contact:{
					nombre: '',
					telefono: '',
					fecha_nacimiento: '',
					direccion: '',
					correo: '',
				},
				busqueda:'',
				id:0,
				update: true,
				modal:0,
				titleModal: ' ',
				contacts:[],
			}
		},
		methods: {
			async list () {
				const res = await axios.get('contacts');
				this.contacts = res.data;
			},
			async eliminar (id) {
				//console.log(id);
				const res = await axios.delete('contacts/'+id);
				this.list();
			},
			async save () {
				if(this.update){
					const res = await axios.put('contacts/'+this.id, this.contact);
					//console.log(res);
				}else{
					const res = await axios.post('contacts',this.contact);					
				}

				this.closeModal();
				this.list();
				
				
			},
			async search () {
				if(this.busqueda != ''){
					const res = await axios.get('contacts/search/'+this.busqueda);
					this.contacts = res.data;
				}else{
					this.list();
				}
				
				
				
				
			},
			openModal (data = {}) {
				this.modal = 1;
				if(this.update){
					this.id = data.id;
					this.titleModal = 'Modificar Contacto';
					this.contact.nombre = data.nombre;
					this.contact.telefono = data.telefono;
					this.contact.fecha_nacimiento = data.fecha_nacimiento;
					this.contact.direccion = data.direccion;
					this.contact.correo = data.correo;
				}else{
					this.id = 0;
					this.titleModal = 'Crear Contacto';
					this.contact.nombre = '';
					this.contact.telefono = '';
					this.contact.fecha_nacimiento = '';
					this.contact.direccion = '';
					this.contact.correo = '';
				}
			},
			closeModal () {
				this.modal = 0;
			},
		},
		created(){
			this.list();
		}
	}
</script>

<style>
	.show {
		display: list-item;
		opacity:1;
		background: rgba(44,38,75,0.849);
	}
</style>
