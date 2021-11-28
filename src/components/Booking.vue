<template>
  <div class="row">
    <div class="col s12">
      <div class="container">
        <div class="card" v-show="show">
          <div class="card-content">
            <div class="row">
              <div class="col s12 m12" v-for="data in events" :key="data.id">
                <div class="card blue-grey darken-1">
                  <div class="card-content white-text">
                    <span class="card-title"
                      ><em>{{ data.description }}</em></span
                    >
                    <p>
                      <em>FECHA: {{ data.date }}</em>
                    </p>
                    <p>
                      <em
                        >BOLETERÍA DISPONIBLE:
                        <b>{{ data.availability }}</b></em
                      >
                    </p>
                    <p>
                      <em>RECUERDA RESPETAR LOS PROTOCOLOS DE BIOSEGURIDAD</em>
                    </p>
                  </div>
                  <div class="card-action blue-grey darken-2">
                    <a style="cursor: pointer" v-on:click="booking(data)"
                      >RESERVAR</a
                    >
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="card" v-show="!show">
          <div class="card-content">
            <div class="row">
              <div class="col s12 m12 l12">
                <h5 class="blue-text" align="left">
                  <strong
                    ><em
                      ><b>RESERVA {{ event.description }}</b></em
                    ></strong
                  >
                </h5>
              </div>
            </div>
            <div class="row">
              <div class="input-field col s6">
                <i class="material-icons prefix">recent_actors</i>
                <input
                  id="dni"
                  type="text"
                  v-model="buyer.dni"
                  v-on:blur="validate"
                />
                <label for="dni">Dni</label>
              </div>
              <div class="input-field col s6">
                <i class="material-icons prefix">filter_1</i>
                <input id="first_name" type="text" v-model="buyer.first_name" />
                <label for="first_name">Primer nombre</label>
              </div>
            </div>
            <div class="row">
              <div class="input-field col s6">
                <i class="material-icons prefix">filter_2</i>
                <input id="last_name" type="text" v-model="buyer.last_name" />
                <label for="last_name">Segundo nombre</label>
              </div>
              <div class="input-field col s6">
                <i class="material-icons prefix">filter_3</i>
                <input
                  id="first_surname"
                  type="text"
                  v-model="buyer.first_surname"
                />
                <label for="first_surname">Primer apellido</label>
              </div>
            </div>
            <div class="row">
              <div class="input-field col s6">
                <i class="material-icons prefix">filter_4</i>
                <input
                  id="last_surname"
                  type="text"
                  v-model="buyer.last_surname"
                />
                <label for="last_surname">Segundo apellido</label>
              </div>
              <div class="input-field col s6">
                <i class="material-icons prefix">email</i>
                <input id="email" type="email" v-model="buyer.email" />
                <label for="email">Email</label>
              </div>
            </div>
            <div class="row">
              <div class="input-field col s6">
                <i class="material-icons prefix">phone</i>
                <input id="phone" type="text" v-model="buyer.phone" />
                <label for="phone">Teléfono</label>
              </div>
              <div class="input-field col s6">
                <i class="material-icons prefix">art_track</i>
                <input
                  id="ticket"
                  type="number"
                  min="0"
                  :max="event.availability"
                  v-model="buyer.ticket"
                />
                <label for="ticket"
                  >Numero de boletas (Disponibles
                  {{ event.availability }})</label
                >
              </div>
            </div>
            <div class="row">
              <div class="row" v-if="events_registered != ''">
                <div class="col s12 m12 l12">
                  <h5 class="blue-text" align="left">
                    <strong
                      ><em><b>MIS RESERVAS</b></em></strong
                    >
                  </h5>
                </div>
              </div>
              <table
                class="striped highlight centered responsive-table"
                v-if="events_registered != ''"
              >
                <thead>
                  <tr class="blue darken-4 white-text">
                    <th>
                      <b><em>Evento</em></b>
                    </th>
                    <th>
                      <b><em>Fecha</em></b>
                    </th>
                    <th>
                      <b><em>Boletas</em></b>
                    </th>
                    <th>
                      <b><em>Fecha de reservación</em></b>
                    </th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="data in events_registered" :key="data.id">
                    <td>{{ data.events.description }}</td>
                    <td>{{ data.events.date }}</td>
                    <td>
                      <a>{{ data.tickets }}</a>
                    </td>
                    <td>
                      <a>{{ data.created_at }}</a>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
            <br />
            <div class="progress" id="progress">
              <div class="indeterminate"></div>
            </div>
            <div class="row">
              <div class="col s12 display-flex justify-content-end mt-1">
                <button
                  type="submit"
                  class="btn indigo"
                  id="save"
                  v-on:click="save"
                >
                  Guardar
                </button>
                <button
                  type="button"
                  class="btn btn-light red"
                  id="clean"
                  v-on:click="clean"
                >
                  Cancelar
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import axios from "axios";
const $ = require("jquery");
export default {
  data() {
    return {
      buyer: {
        id: "",
        dni: "",
        first_name: "",
        last_name: "",
        first_surname: "",
        last_surname: "",
        phone: "",
        email: "",
        event: "",
        ticket: "",
      },
      events: [],
      show: true,
      event: "",
      events_registered: [],
    };
  },
  methods: {
    oauthToken: function () {
      axios
        .post("http://127.0.0.1:8000/api/oauth/token", {
          grant_type: "password",
          client_id: "3",
          client_secret: "HbwPtE0EHPK0CNGqXIvkNpbcuOJCYSBeFXW235OM",
          username: "juandaleja@gmail.com",
          password: "1234567890",
        })
        .then((result) => {
          window.localStorage.setItem("_token", result.data.access_token);
          this.getData();
        });
    },
    getData: function () {
      axios
        .get("http://127.0.0.1:8000/api/get-data", {
          headers: {
            Authorization: "Bearer " + window.localStorage.getItem("_token"),
          },
        })
        .then((result) => {
          this.events = result.data;
        });
    },
    validate: function () {
      this.buyer = {
        id: "",
        dni: this.buyer.dni,
        first_name: "",
        last_name: "",
        first_surname: "",
        last_surname: "",
        phone: "",
        email: "",
        event: this.buyer.event,
        ticket: "",
      };
      this.events_registered = [];
      axios
        .post(
          "http://127.0.0.1:8000/api/validate",
          {
            record: this.buyer.dni,
          },
          {
            headers: {
              Authorization: "Bearer " + window.localStorage.getItem("_token"),
            },
          }
        )
        .then((result) => {
          if (result.data.data != null) {
            this.events_registered = [];
            this.preload(result.data.data);
            this.events_registered = result.data.bookings;
          }
        });
    },
    preload: function (data) {
      this.buyer = {
        id: data.id,
        dni: data.dni,
        first_name: data.first_name,
        last_name: data.last_name,
        first_surname: data.first_surname,
        last_surname: data.last_surname,
        phone: data.phone,
        email: data.email,
        event: this.buyer.event,
        ticket: "",
      };
      $("label").addClass("active");
      M.toast({
        html: "Datos precargados correctamente",
        classes: "rounded green",
      });
    },
    booking: function (data) {
      this.buyer.event = data.id;
      if (data.availability > 0) {
        this.show = false;
        this.event = data;
      } else {
        M.toast({
          html: "Sin Boletería Disponible",
          classes: "rounded orange",
        });
      }
    },
    clean: function () {
      this.buyer = {
        id: "",
        dni: "",
        first_name: "",
        last_name: "",
        first_surname: "",
        last_surname: "",
        phone: "",
        email: "",
        event: "",
        ticket: "",
      };
      this.show = true;
      this.event = "";
      this.events_registered = [];
    },
    save: function () {
      if (this.buyer.dni == "") {
        M.toast({
          html: "Campo Dni, es requerido",
          classes: "rounded orange",
        });
        $("#dni").focus();
        return;
      }
      if (this.buyer.first_name == "") {
        M.toast({
          html: "Campo Primer nombre, es requerido",
          classes: "rounded orange",
        });
        $("#first_name").focus();
        return;
      }
      if (this.buyer.first_surname == "") {
        M.toast({
          html: "Campo Primer apellido, es requerido",
          classes: "rounded orange",
        });
        $("#first_surname").focus();
        return;
      }
      if (this.buyer.email == "") {
        M.toast({
          html: "Campo Email, es requerido",
          classes: "rounded orange",
        });
        $("#email").focus();
        return;
      }
      if (this.buyer.phone == "") {
        M.toast({
          html: "Campo Teléfono, es requerido",
          classes: "rounded orange",
        });
        $("#phone").focus();
        return;
      }
      if (this.buyer.ticket == "") {
        M.toast({
          html: "Campo Numero de boletas, es requerido",
          classes: "rounded orange",
        });
        $("#ticket").focus();
        return;
      }
      if (this.buyer.ticket > this.event.availability) {
        M.toast({
          html: "El numero de boletas a reservar no puede ser superior al disponible",
          classes: "rounded red",
        });
        return;
      }
      $("#save").addClass("disabled");
      $("#progress").show();
      axios
        .post(
          "http://127.0.0.1:8000/api/save",
          {
            record: this.buyer,
          },
          {
            headers: {
              Authorization: "Bearer " + window.localStorage.getItem("_token"),
            },
          }
        )
        .then((result) => {
          if (result.data.status == "save") {
            $("#progress").hide();
            $("#save").removeClass("disabled");
            this.clean();
            this.oauthToken();
            swal("¡FELICIDADES!", "¡Datos guardados correctamente!", "success");
          }
        });
    },
  },
  mounted() {
    this.oauthToken();
    $("#progress").hide();
  },
};
</script>
<style>
</style>