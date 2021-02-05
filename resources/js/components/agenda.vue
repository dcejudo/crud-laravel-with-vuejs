<template>
  <div>
    <h1 class="text-center">Agenda Telefónica</h1>
    <hr />

    <!-- Button to Open the Modal -->
    <button @click="modificar=false; abrirModal();" type="button" class="btn btn-success my-4">Nuevo contacto</button>

    <!-- The Modal -->
    <div class="modal" :class="{mostrar: modal}">
      <div class="modal-dialog">
        <div class="modal-content">
          <!-- Modal Header -->
          <div class="modal-header">
            <h4 class="modal-title">{{tituloModal}}</h4>
            <button @click="cerrarModal();"  type="button" class="close" data-dismiss="modal">
              &times;
            </button>
          </div>

          <!-- Modal body -->
          <div class="modal-body">
            <div class="my-4">
              <label for="nombre">Nombre</label>
              <input v-model="agenda.nombre" type="text" class="form-control" id="nombre" placeholder="Nombre">
            </div>
            <div class="my-4">
              <label for="telefono">Teléfono</label>
              <input v-model="agenda.telefono" type="text" class="form-control" id="telefono" placeholder="Teléfono">
            </div>
            <div class="my-4">
              <label for="correo">Correo</label>
              <input v-model="agenda.correo" type="text" class="form-control" id="correo" placeholder="Correo">
            </div>
          </div>

          <!-- Modal footer -->
          <div class="modal-footer">
            <button @click="cerrarModal();" type="button" class="btn btn-secondary" data-dismiss="modal">
              Cancelar
            </button>
            <button  @click="guardar();" type="button" class="btn btn-success" data-dismiss="modal">
              Guardar
            </button>


          </div>
        </div>
      </div>
    </div>
          <!-- Start Table -->

    <table class="table">
      <thead class="table-info">
        <tr>
          <th scope="col">#</th>
          <th scope="col">Nombre</th>
          <th scope="col">Teléfono</th>
          <th scope="col">Correo Electrónico</th>
          <th scope="col" colspan="2" class="text-center">Accion</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="art in agendas" :key="art.id">
          <th scope="row">{{ art.id }}</th>
          <td>{{ art.nombre }}</td>
          <td>{{ art.telefono }}</td>
          <td>{{ art.correo }}</td>
          <td class="text-center">
            <button  @click="modificar=true; abrirModal(art);" class="btn btn-success">Editar</button>
            <button @click="eliminar(art.id)" class="btn btn-danger">
              Eliminar
            </button>
          </td>
        </tr>
      </tbody>
    </table>
              <!-- Finish Table -->

  </div>
</template>

<script>
export default {
  data() {
    return {
      agenda:{
        nombre:'',
        telefono:'',
        correo:'',
      },
      id:0,
      modificar:true,
      modal:0,
      tituloModal:'',
      agendas: [],
    };
  },
  methods: {
    async listar() {
      const res = await axios.get('/agendas');
      this.agendas = res.data;
    },
    async eliminar(id) {
      const res = await axios.delete('/agendas/' + id);
      this.listar();
    },
    async guardar() {
      if(this.modificar){
        const res = await axios.put('/agendas/'+this.id, this.agenda);
        // console.log(this.id);
      }else{
        const res = await axios.post('/agendas/', this.agenda);
      }
      this.cerrarModal();
      this.listar();
    },
    abrirModal(data={}){
      this.modal=1;
      if(this.modificar){
        this.id=data.id;
        this.tituloModal="Modificar agenda";
        this.agenda.nombre=data.nombre;
        this.agenda.telefono=data.telefono;
        this.agenda.correo=data.correo;
      }else{
        this.id=0;
        this.tituloModal="Crear agenda";
        this.agenda.nombre='';
        this.agenda.telefono='';
        this.agenda.correo='';
      }
    },
    cerrarModal(){
      this.modal=0;
    },
  },
  created() {
    this.listar();
  },
};
</script>

<style>
  .mostrar{
    display: list-item;
    opacity: 1;
    background: rgba(39, 40, 51, 0.705);
  }
</style>