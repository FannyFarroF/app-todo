<template>
  <div class="home">
    <div class="container">
      <div class="row py-5">
        <h2 class="title-section">#todo</h2>
        <div class="tab-home my-5 w-50 m-auto">
          <nav>
            <div
              class="nav nav-tabs justify-content-center"
              id="nav-tab"
              role="tablist"
            >
              <button
                class="nav-link active px-5"
                id="nav-home-tab"
                data-bs-toggle="tab"
                data-bs-target="#nav-home"
                type="button"
                role="tab"
                aria-controls="nav-home"
                aria-selected="true"
              >
                Todos
              </button>
              <button
                class="nav-link px-5"
                id="nav-profile-tab"
                data-bs-toggle="tab"
                data-bs-target="#nav-profile"
                type="button"
                role="tab"
                aria-controls="nav-profile"
                aria-selected="false"
              >
                Activas
              </button>
              <button
                class="nav-link px-5"
                id="nav-contact-tab"
                data-bs-toggle="tab"
                data-bs-target="#nav-contact"
                type="button"
                role="tab"
                aria-controls="nav-contact"
                aria-selected="false"
              >
                Completadas
              </button>
            </div>
          </nav>
          <div class="tab-content py-4" id="nav-tabContent">
            <div
              class="tab-pane fade show active"
              id="nav-home"
              role="tabpanel"
              aria-labelledby="nav-home-tab"
            >
              <SearchComponent
                v-on:nuevaTarea="agregarTarea($event)"
                v-bind:seAgrego="seAgrego"
              />

              <div class="list my-4">
                <ul class="p-0">
                  <li class="mb-3" v-for="(item, index) in data" :key="index">
                    <div class="form-check text-start">
                      <input
                        class="form-check-input"
                        type="checkbox"
                        value="true"
                        :id="'flexCheckDefault' + index"
                        v-model="data[index].estado"
                        @change="actualizarEstado(item, $event)"
                      />
                      <label
                        class="form-check-label"
                        :for="'flexCheckDefault' + index"
                        v-bind:class="
                          data[index].estado == true ? 'completed' : ''
                        "
                      >
                        {{ item.tarea }}
                      </label>
                    </div>
                  </li>
                </ul>
              </div>
            </div>
            <div
              class="tab-pane fade"
              id="nav-profile"
              role="tabpanel"
              aria-labelledby="nav-profile-tab"
            >
              <SearchComponent
                v-on:nuevaTarea="agregarTarea($event)"
                v-bind:seAgrego="seAgrego"
              />

              <div class="list my-4">
                <ul class="p-0">
                  <li
                    class="mb-3"
                    v-for="(item, index) in soloactivos"
                    :key="index"
                  >
                    <div class="form-check text-start">
                      <input
                        class="form-check-input"
                        type="checkbox"
                        :value="soloactivos[index].estado"
                        :checked="soloactivos[index].estado == true"
                        :id="'flexCheckDefaultAct' + item.id"
                        v-model="soloactivos[index].estado"
                        @change="actualizarEstado(item, $event)"
                      />
                      <label
                        class="form-check-label mx-3"
                        :for="'flexCheckDefaultAct' + item.id"
                      >
                        {{ item.tarea }}
                      </label>
                    </div>
                  </li>
                </ul>
              </div>
            </div>

            <div
              class="tab-pane fade"
              id="nav-contact"
              role="tabpanel"
              aria-labelledby="nav-contact-tab"
            >
              <div class="list my-4">
                <ul class="p-0">
                  <li
                    class="mb-3"
                    v-for="(item, index) in soloCompletados"
                    :key="index"
                  >
                    <div
                      class="d-flex justify-content-between align-items-center"
                    >
                      <div class="form-check text-start">
                        <input
                          type="checkbox"
                          class="form-check-input"
                          :id="'flexCheckDefault' + index"
                          checked
                          disabled
                        />
                        <label
                          class="custom-control-label mx-3"
                          v-bind:class="
                            soloCompletados[index].estado == true
                              ? 'completed'
                              : ''
                          "
                        >
                          {{ item.tarea }}
                        </label>
                      </div>

                      <div class="icon" @click="eliminarTarea(item)">
                        <span class="material-icons">
                          delete_outline
                        </span>
                      </div>
                    </div>
                  </li>
                </ul>
                <div
                  class="another-element float-right"
                  v-show="soloCompletados.length >= 1"
                >
                  <button
                    class="btn btn-eliminar d-flex align-items-center"
                    @click="eliminarTodos()"
                  >
                    <span class="material-icons mx-2">
                      delete_outline
                    </span>
                    Eliminar todos
                  </button>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  // @ is an alias to /src
  import SearchComponent from "@/components/SearchComponent.vue";

  export default {
    name: "Home",
    components: {
      SearchComponent,
    },
    data() {
      return {
        data: [],
        seAgrego: false,
      };
    },
    mounted() {
      this.contentLoaded();
    },
    watch: {
      data: function() {
        this.soloactivos;
        this.soloCompletados;
      },
    },
    methods: {
      contentLoaded() {
        this.data = JSON.parse(localStorage.getItem("tareas")) || [];
      },
      agregarTarea(value) {
        let nueva = {
          id: Date.now(),
          tarea: value,
          estado: false,
        };
        this.data.push(nueva);
        this.seAgrego = true;

        this.sincronizarLocalStorage();
      },
      actualizarEstado(itemActualizar, e) {
        e.preventDefault();

        console.log("actualizar", itemActualizar);

        this.data.forEach((item) => {
          if (item.id == itemActualizar.id) {
            item.estado = itemActualizar.estado;
          }
        });
        this.sincronizarLocalStorage();
      },
      eliminarTarea(itemEliminar) {
        this.data = this.data.filter((item) => item.id != itemEliminar.id);

        this.sincronizarLocalStorage();
      },
      eliminarTodos() {
        this.data = this.data.filter((item) => item.estado == false);
        this.sincronizarLocalStorage();
      },
      sincronizarLocalStorage() {
        localStorage.setItem("tareas", JSON.stringify(this.data));
      },
    },
    computed: {
      soloactivos() {
        var datos = this.data;

        if (datos) {
          return this.data.filter((item) => item.estado == false);
        } else {
          return (datos = []);
        }
      },
      soloCompletados() {
        var datos = this.data;

        if (datos) {
          return this.data.filter((item) => item.estado === true);
        } else {
          return (datos = []);
        }
      },
    },
  };
</script>

<style lang="scss" scoped>
  .title-section {
    color: #333;
    font-size: 36px;
  }
  .another-element {
    float: right;
  }
  ul {
    li {
      list-style: none;
      .completed {
        text-decoration: line-through;
      }
      .icon {
        color: #bdbdbd;
        cursor: pointer;
      }
    }
  }
  .btn-eliminar {
    background: #eb5757;
    color: #fff;
  }
  .btn-eliminar:hover {
    background: #eb5757;
    color: #fff;
    opacity: 0.89;
  }
  .nav-link {
    border-top: 1px solid transparent !important;
    border-left: 1px solid transparent !important;
    border-right: 1px solid transparent !important;
  }
  .nav-tabs .nav-item.show .nav-link,
  .nav-tabs .nav-link.active {
    color: #495057;
    border: 0;
    border-bottom: 5px solid #2f80ed;
  }
</style>
