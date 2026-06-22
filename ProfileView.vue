<script setup>
import { ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import Sidebar from '@/components/Sidebar.vue'
import Login from '@/components/Login.vue'

const router = useRouter()

const sidebarMinimized = ref(false)

const handleToggleSidebar = (isMinimized) => {
  sidebarMinimized.value = isMinimized
}

const showLogin = ref(false)

const user = ref({
  name: '',
  email: '',
  avatar: ''
})

onMounted(() => {
  const currentUser = localStorage.getItem('currentUser')

  if (currentUser) {
    user.value = JSON.parse(currentUser)
  }
})

const guardarPerfil = () => {

  localStorage.setItem(
    'currentUser',
    JSON.stringify(user.value)
  )

  alert('Perfil actualizado')
}

const cerrarSesion = () => {

  localStorage.removeItem('currentUser')

  alert('Sesión cerrada')

  router.push('/')
}
</script>


<template>
  <div class="page-shell">

    <Sidebar
      @openLogin="showLogin = true"
      @toggleSidebar="handleToggleSidebar"
    />

    <main :class="['main-content', { 'sidebar-minimized': sidebarMinimized }]">

      <div class="profile-container">

        <div class="profile-card">

          <img
            :src="user.avatar"
            class="avatar"
            alt="Foto de perfil"
          />

          <h1>Mi Perfil</h1>

          <label>Nombre</label>
          <input
            v-model="user.name"
            type="text"
          />

          <label>Email</label>
          <input
            v-model="user.email"
            type="email"
          />

          <button
            class="save-btn"
            @click="guardarPerfil"
          >
            Guardar cambios
          </button>

          <button
            class="logout-btn"
            @click="cerrarSesion"
          >
            Cerrar sesión
          </button>

        </div>

      </div>

    </main>

    <Login
      v-if="showLogin"
      @close="showLogin = false"
    />

  </div>
</template>


<style scoped>

.main-content{
  margin-left:280px;
  width:calc(100% - 280px);
  min-height:100vh;
  padding:40px;
  background:#0f172a;
  transition:.3s;
}

.main-content.sidebar-minimized{
  margin-left:80px;
  width:calc(100% - 80px);
}

.profile-container{
  display:flex;
  justify-content:center;
  align-items:center;
  min-height:90vh;
}

.profile-card{
  width:550px;
  max-width:95%;
  background:#111827;
  border-radius:25px;
  padding:40px;
  box-shadow:0 15px 35px rgba(0,0,0,.4);
}

.avatar{
  width:140px;
  height:140px;
  border-radius:50%;
  object-fit:cover;
  display:block;
  margin:0 auto 25px;
  border:4px solid #BF924B;
}

h1{
  text-align:center;
  color:white;
  margin-bottom:30px;
}

label{
  display:block;
  color:#d1d5db;
  margin:18px 0 8px;
}

input{
  width:100%;
  padding:15px;
  border:none;
  border-radius:12px;
  background:#1f2937;
  color:white;
  font-size:15px;
  box-sizing:border-box;
}

.save-btn{
  width:100%;
  margin-top:30px;
  padding:15px;
  border:none;
  border-radius:12px;
  cursor:pointer;
  font-weight:bold;
  background:linear-gradient(135deg,#BF924B,#F3DDA7);
  color:black;
}

.logout-btn{
  width:100%;
  margin-top:15px;
  padding:15px;
  border:none;
  border-radius:12px;
  cursor:pointer;
  font-weight:bold;
  background:#dc2626;
  color:white;
}

.save-btn:hover{
  transform:translateY(-2px);
}

.logout-btn:hover{
  background:#b91c1c;
}

</style>
