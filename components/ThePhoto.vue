<template>
  <div class="container">
    <div class="center">
      <button class="btn btn-halloween" @click="showForm = true">Participer au concours Halloween !</button>
    </div>
    <form v-if="showForm" @submit.prevent="submitForm" class="my-form">
      <div class="form-group">
        <label for="Nom">Nom :</label>
        <input type="text" id="Nom" class="form-control" v-model="formData.Nom" required>
      </div>
      <div class="form-group">
        <label for="Prenom">Prénom :</label>
        <input type="text" id="Prenom" class="form-control" v-model="formData.Prenom" required>
      </div>
      <div class="form-group">
        <label for="Email">Email :</label>
        <input type="email" id="Email" class="form-control" v-model="formData.Email" required>
      </div>
      <div class="form-group">
        <label for="Telephone">Téléphone :</label>
        <input type="text" id="Telephone" class="form-control" v-model="formData.Telephone" required>
      </div>
      <div class="form-group">
        <label for="Link">Image :</label>
        <input type="file" id="Link" class="form-control" @change="onFileChange" required>
      </div>
      <button type="submit" class="btn btn-light">Envoyer</button>
    </form>
  </div>
  <div  class="row">
    <div v-for="photo in photosAccueil" class="card" :key="photo.id">
      <NuxtLink :to="`/photovote/?id=${photo.id}`"><img :src="getPhotoURL(photo.Photo)" class="card-img-top" alt="Photo"> </NuxtLink>

    </div>
  </div>
</template>
<!-- ... -->


<script>
import axios from 'axios';

export default {
  data() {
    return {
      showForm: false, // Initialisé à false pour masquer le formulaire
      photosAccueil: [], // Assurez-vous que cette ligne existe
      formData: {
        Nom: '',
        Prenom: '',
        Email: '',
        Telephone: '',
        Link: null,
      },
    };
  },
  mounted() {
    axios.get('https://studiophotov2-d849983bf69e.herokuapp.com/photo')
      .then(response => {
        this.loading = false;
        this.photos = response.data; // Toutes les photos
        console.log(this.photos);

        // Filtrer les photos avec Categorie_id = 1 pour la page d'accueil
        this.photosAccueil = this.photos.filter(photo => photo.Categorie_id === 1);
        console.log(this.photosAccueil);
      })
      .catch(error => {
        console.error('Error:', error);
      });

    // Récupérez les catégories depuis l'API
    axios.get('https://studiophotov2-d849983bf69e.herokuapp.com/categorie')
      .then(response => {
        this.categories = response.data;
      })
      .catch(error => {
        console.error('Erreur lors de la récupération des catégories :', error);
      });
  },
  methods: {
    getPhotoURL(base64Data) {
      const binaryData = atob(base64Data);
      const mimeType = 'image/jpeg'; // à adapter en fonction du type de données
      const arrayBuffer = new ArrayBuffer(binaryData.length);
      const uint8Array = new Uint8Array(arrayBuffer);
      for (let i = 0; i < binaryData.length; i++) {
        uint8Array[i] = binaryData.charCodeAt(i);
      }
      const blob = new Blob([arrayBuffer], { type: mimeType });
      const urlCreator = window.URL || window.webkitURL;
      const imageUrl = urlCreator.createObjectURL(blob);
      return imageUrl;
    },
    onFileChange(event) {
      this.formData.Link = event.target.files[0];
    },
    async submitForm() {
      const formData = new FormData();
      for (const key in this.formData) {
        formData.append(key, this.formData[key]);
      }
      try {
        const response = await axios.post('https://studiophotov2-d849983bf69e.herokuapp.com/photo', formData);
        if (response.data === 'success') {
          alert('Photo ajoutée avec succès.');
          // Réinitialisez le formulaire si nécessaire
          // this.formData = { Nom: '', Prenom: '', Email: '', Telephone: '', Link: null };
        } else {
          alert('Une erreur s\'est produite lors de l\'ajout de la photo.');
        }
      } catch (error) {
        console.error(error);
        alert('Une erreur s\'est produite lors de la requête.');
      }
    },
  },
};
</script>
<style scoped>
@media only screen and (min-width: 767px) {
.card {
    border: none !important;
    margin-top: 10px;
    width: auto;
    height: auto;
  }

  img {
    width: 200px;
    height: 200px;
  }

  img:hover {
    opacity: 1;
    transition: 0.5s;
    transform: rotateY(0deg) scale(1.1);
    box-shadow: 1px 1px 1px 1px rgba(0, 0, 0, 0.212);
    position: inherit;
    z-index: 1;
  }

.container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;

}

.center {
  text-align: center;
}

.btn-halloween {
  background-color: #ff6600;
  /* Couleur orange Halloween */
  color: #fff;
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  font-size: 24px;
  /* Taille de la police */
  font-weight: bold;
  text-transform: uppercase;
  cursor: pointer;
  box-shadow: 3px 3px 5px rgba(0, 0, 0, 0.5);
  /* Ombre */
  transition: background-color 0.3s;
}

.btn-halloween:hover {
  background-color: #ff3300;
  /* Couleur orange Halloween plus foncée au survol */
}


.my-form {
  margin-top: 10px;
  background-color: #000;
  /* Fond noir pour un style sombre */
  color: #ff6600;
  /* Texte orange Halloween */
  padding: 20px;
  border-radius: 10px;
  box-shadow: 3px 3px 5px rgba(0, 0, 0, 0.5);
}

.my-form .form-group label {
  font-weight: bold;
  color: #ff6600;
}

.my-form .form-control {
  background-color: #222;
  /* Fond sombre pour les champs de formulaire */
  color: #fff;
  border: 2px solid #ff6600;
}

.my-form .form-control:focus {
  border-color: #ff3300;
  /* Couleur de bordure orange au focus */
}}
@media only screen and (max-width: 767px) {
  .card {
    border: none !important;
    margin-top: 10px;
    width: auto;
    height: auto;
  }

  img {
    width: 200px;
    height: 200px;
  }

  img:hover {
    opacity: 1;
    transition: 0.5s;
    transform: rotateY(0deg) scale(1.1);
    box-shadow: 1px 1px 1px 1px rgba(0, 0, 0, 0.212);
    position: inherit;
    z-index: 1;
  }

.container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;

}

.center {
  text-align: center;
}

.btn-halloween {
  background-color: #ff6600;
  /* Couleur orange Halloween */
  color: #fff;
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  font-size: 24px;
  /* Taille de la police */
  font-weight: bold;
  text-transform: uppercase;
  cursor: pointer;
  box-shadow: 3px 3px 5px rgba(0, 0, 0, 0.5);
  /* Ombre */
  transition: background-color 0.3s;
}

.btn-halloween:hover {
  background-color: #ff3300;
  /* Couleur orange Halloween plus foncée au survol */
}


.my-form {
  margin-top: 10px;
  background-color: #000;
  /* Fond noir pour un style sombre */
  color: #ff6600;
  /* Texte orange Halloween */
  padding: 20px;
  border-radius: 10px;
  box-shadow: 3px 3px 5px rgba(0, 0, 0, 0.5);
}

.my-form .form-group label {
  font-weight: bold;
  color: #ff6600;
}

.my-form .form-control {
  background-color: #222;
  /* Fond sombre pour les champs de formulaire */
  color: #fff;
  border: 2px solid #ff6600;
}

.my-form .form-control:focus {
  border-color: #ff3300;
  /* Couleur de bordure orange au focus */
}
}
</style>