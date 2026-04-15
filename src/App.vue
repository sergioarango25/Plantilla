<template>

<!-- 🔐 PANTALLA INICIAL -->
<div v-if="vista==='inicio'" class="inicio">
  <div class="cardInicio">
    <h1>Serviteka 🔧</h1>
    <p>Bienvenido a tu taller digital</p>

    <button class="btn" @click="vista='login'">Iniciar sesión</button>
    <button class="btn register" @click="vista='registro'">Registrarse</button>
    <button class="btn ghost" @click="vista='app'">Continuar sin cuenta</button>
  </div>
</div>

<!-- 🔐 LOGIN -->
<div v-else-if="vista==='login'" class="auth">
  <div class="cardAuth">
    <h2>Iniciar sesión</h2>
    <input v-model="correo" placeholder="Correo electrónico">
    <input type="password" placeholder="Contraseña">

    <button @click="login">Entrar</button>

    <p @click="vista='registro'" class="link">¿No tienes cuenta? Regístrate</p>
  </div>
</div>

<!-- 🔐 REGISTRO -->
<div v-else-if="vista==='registro'" class="auth">
  <div class="cardAuth">
    <h2>Crear cuenta</h2>
    <input v-model="correo" placeholder="Correo electrónico">
    <input type="password" placeholder="Contraseña">

    <button @click="registro">Registrarse</button>

    <p @click="vista='login'" class="link">¿Ya tienes cuenta? Inicia sesión</p>
  </div>
</div>

<!-- 🛒 CARRITO -->
<CarritoView 
  v-else-if="vista==='carrito'"
  :carrito="carrito"
  :total="total"
  @volver="vista='app'"
  @incrementar="carrito[$event].cantidad++"
  @decrementar="carrito[$event].cantidad>1?carrito[$event].cantidad--:carrito.splice($event,1)"
  @eliminar="carrito.splice($event,1)"
  @login="vista='login'"
/>

<!-- 🏠 APP ORIGINAL -->
<div v-else>

<header class="header">
  <h1 class="titulo">Taller El Volante</h1>

  <div class="acciones">
    <button class="btnGrande">Filtro</button>

    <button class="btnGrande" @click="vista='carrito'">
      Carrito ({{totalItems}})
    </button>

    <button class="btnGrande" @click="showM=!showM">Menu</button>
  </div>
</header>

<section class="buscador">
  <input v-model="buscar" placeholder="Buscar..."/>
</section>

<div class="layout">

  <aside class="menu" v-if="showM">
    <h3>Categorías</h3>
    <button class="categoria" v-for="c in cats" :key="c" @click="cat=c">
      {{c||'Todos'}}
    </button>
  </aside>

  <main class="contenido">

    <section class="destacado">
      <button class="flecha" @click="i=(i-1+productos.length)%productos.length">◀</button>

      <div class="cardGrande">
        <img :src="productos[i].imagen">
        <h2>{{productos[i].nombre}}</h2>
        <p class="desc">{{productos[i].descripcion}}</p>
        <p class="precio">$ {{productos[i].precio}}</p>
        <button class="agregar" @click="add(productos[i])">
          Agregar al carrito
        </button>
      </div>

      <button class="flecha" @click="i=(i+1)%productos.length">▶</button>
    </section>

    <section class="productos">
      <div class="card" v-for="p in filtrados" :key="p.id" @click="detalle=p">
        <div v-if="p.etiqueta" class="etiqueta">{{p.etiqueta}}</div>

        <img :src="p.imagen">
        <h3>{{p.nombre}}</h3>
        <p class="desc">{{p.descripcion}}</p>
        <p class="precio">$ {{p.precio}}</p>

        <button class="agregar" @click.stop="add(p)">
          Agregar
        </button>
      </div>
    </section>

  </main>
</div>

<div v-if="detalle" class="modal">
  <div class="modalContenido">
    <img :src="detalle.imagen">
    <h2>{{detalle.nombre}}</h2>
    <p>{{detalle.descripcion}}</p>
    <p class="precio">$ {{detalle.precio}}</p>
    <button class="agregar" @click="add(detalle)">Agregar al carrito</button>
    <button class="cerrar" @click="detalle=null">Cerrar</button>
  </div>
</div>

</div>

</template>

<script>
import { productos } from '@/data/productos'
import CarritoView from '@/components/CarritoView.vue'

export default {
  components:{ CarritoView },

  data(){return{
    vista:'inicio', // 👈 IMPORTANTE (pantalla inicial)
    correo:"",
    productos, carrito:[], buscar:"", i:0,
    showM:true, cat:"", detalle:null,
    cats:["","Frenos","Motor","Suspensión","Electricidad","Llantas"]
  }},

  computed:{
    filtrados(){return this.productos.filter(p=>
      p.nombre.toLowerCase().includes(this.buscar.toLowerCase()) &&
      (!this.cat||p.categoria===this.cat)
    )},
    totalItems(){return this.carrito.reduce((t,p)=>t+p.cantidad,0)},
    total(){return this.carrito.reduce((t,p)=>t+(p.precio*p.cantidad),0)}
  },

  methods:{
    add(p){
      const e=this.carrito.find(c=>c.id===p.id)
      e ? e.cantidad++ : this.carrito.push({...p,cantidad:1})
    },

    login(){
      this.vista='app'
    },

    registro(){
      this.vista='app'
    }
  }
}
</script>

<style scoped>
/* 🔐 inicio */
.inicio {
  height:100vh;
  display:flex;
  justify-content:center;
  align-items:center;
  background: linear-gradient(#111,#333);
}

.cardInicio {
  background:white;
  padding:40px;
  border-radius:12px;
  text-align:center;
}

.btn {
  display:block;
  width:100%;
  margin:10px 0;
  padding:10px;
}

.register {
  background:orange;
  color:white;
}

.ghost {
  background:none;
}

/* 🔐 auth */
.auth {
  height:100vh;
  display:flex;
  justify-content:center;
  align-items:center;
  background:#1e1e1e;
}

.cardAuth {
  background:white;
  padding:30px;
  border-radius:10px;
  width:300px;
}

.link {
  color:blue;
  cursor:pointer;
  margin-top:10px;
}
</style>