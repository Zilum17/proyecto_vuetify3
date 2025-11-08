<template>
  <v-main class="bg-grey-lighten-3">
    <v-container fluid>
      <v-row class="ml-14 mr-14">
        <v-col
          cols="12"
          md="2"
        >
          <v-sheet
            min-height="268"
            rounded="lg"
            class="d-flex flex-column align-center pa-4"
          >
            <v-text-field
              hide-details="auto"
              v-model="title"
              label="Titulo"
              class="w-100 mb-4"
              variant="outlined"
            ></v-text-field>
            <v-textarea 
              v-model="text"
              label="Contenido" 
              class="w-100 mb-4"
              variant="outlined"
            ></v-textarea>
            <v-btn color="primary" variant="tonal" @click="addCard" style="width: 50%;">
              <v-icon v-if="!edit" icon="mdi-plus"></v-icon>
              <v-icon v-else icon="mdi-update"></v-icon>
            </v-btn>
          </v-sheet>
        </v-col>

        <v-col
          cols="12"
          md="8"
        >
          <v-sheet
            min-height="70vh"
            rounded="lg"
            class="pa-4"
          >
            <v-row justify="start" align="stretch">
              <v-col
                v-for="(item, idx) in data"
                :key="item.id"
                cols="12"
                sm="12"
                md="6"
                lg="4"
              >
                <v-card
                  variant="elevated"
                  class="mx-auto"
                  max-width="376"
                  max-height="268"
                  :title="item.title"
                >
                  <template v-slot:actions>
                    <v-btn variant="tonal" :text="edit && editIndex === idx ?'Cancelar':'Actualizar'" @click="loadUpdateCard(idx)" :color="edit && editIndex === idx ? 'error' : 'primary'"></v-btn>
                    <v-btn variant="tonal" text="Eliminar" @click="deleteCard(item.id)" color="error"></v-btn>
                  </template>
                  <v-card-text>{{ item.text }}</v-card-text>
                </v-card>
              </v-col>
            </v-row>
          </v-sheet>
        </v-col>

        <v-col
          cols="12"
          md="2"
        >
          <v-sheet
            min-height="268"
            rounded="lg"
          >
            <!--  -->
          </v-sheet>
        </v-col>
      </v-row>
    </v-container>
  </v-main>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { db } from '../firebase/firebase';
import { collection, getDocs, addDoc, updateDoc, deleteDoc, doc } from 'firebase/firestore';

const editDocId = ref(null);
const title = ref('');
const text = ref('');
const edit = ref(false);
const editIndex = ref(-1);
const data = ref([]);

const colRef = collection(db, 'cards'); // Cambia 'tarjetas' al nombre de tu colección

const fetchData = async () => {
  try {
    const snapshot = await getDocs(colRef);
    data.value = snapshot.docs.map(doc => ({
      id: doc.id,
      ...doc.data()
    }));
  } catch (e) {
    console.error('Error al cargar datos:', e);
  }
};

const addCard = async () => {
  if (!title.value || !text.value) return alert('Título y texto son obligatorios');
  
  try {
    if (edit.value && editIndex.value !== -1 && editDocId.value) {
      // Actualizar documento Firestore
      const docRef = doc(db, 'cards', editDocId.value);
      await updateDoc(docRef, {
        title: title.value,
        text: text.value
      });
      edit.value = false;
      editIndex.value = -1;
      editDocId.value = null;
    } else {
      // Agregar nuevo documento a Firestore
      await addDoc(colRef, {
        title: title.value,
        text: text.value
      });
    }
    title.value = '';
    text.value = '';
    fetchData();
  } catch (e) {
    console.error('Error en añadir/actualizar:', e);
  }
};

const deleteCard = async (id) => {
  try {
    await deleteDoc(doc(db, 'cards', id));
    fetchData();
  } catch (e) {
    console.error('Error al eliminar:', e);
  }
};

const loadUpdateCard = (idx) => {
  if (edit.value) {
    cancelEdit();
  } else {
    const item = data.value[idx];
    title.value = item.title;
    text.value = item.text;
    edit.value = true;
    editIndex.value = idx;
    editDocId.value = item.id;
  }
};

const cancelEdit = () => {
  edit.value = false;
  editIndex.value = -1;
  editDocId.value = null;
  title.value = '';
  text.value = '';
};

onMounted(fetchData);


</script>
