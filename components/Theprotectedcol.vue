<template>
    <div>
        <!-- Bouton "Concours Type 1" -->
        <button class="btn btn-primary" @click="showContest1 = !showContest1">Concours Type 1</button>
        <!-- Code à afficher lorsque le bouton est cliqué -->
        <div v-if="showContest1" class="collaps">
            <div class="collaps">
                <div>
                    <p class="pleft">
                        <button class="btn btn-danger" type="button" data-bs-toggle="collapse"
                            data-bs-target="#Ajouterphoto" aria-expanded="false" aria-controls="Ajouterphoto">
                            Ajouter des photos
                        </button>
                    </p>
                    <div style="min-height: 120px; ">
                        <div class="collapse collapse-horizontal" id="Ajouterphoto">
                            <div class="card card-body" style="width: 50%;">
                                <div class="photo">
                                    <form @submit.prevent="updateData">
                                        <label>Prénom :</label>
                                        <input type="text" v-model="Prenom" /> <br>
                                        <label>Nom :</label>
                                        <input type="text" v-model="Nom" /><br>
                                        <label for="FormControlFile">Photo:</label>
                                        <input type="file" class="form-control-file" id="FormControlFile"
                                            @change="onFileChange" /><br>
                                        <button type="submit" class="m-3 btn btn-success">Mettre à jour</button>
                                        <p>Les données seront à jour après 10 minutes sur l'interface client.</p>
                                    </form>
                                    <p>{{ updateMessage }}</p>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>

                <div style="max-height: 25% !important;">
                    <p class="pleft">
                        <button class="btn btn-danger disable" type="button" data-bs-toggle="collapse"
                            data-bs-target="#VoirlesVotes" aria-expanded="false" aria-controls="VoirlesVotes" disabled>
                            Les votes
                        </button>
                    </p>
                    <div>
                        <div class="collapse collapse-horizontal" id="VoirlesVotes" style="display: flex; ">
                            <div v-for="photo in photos" style="margin-left: 2px;">
                                <img style="width: 100%;" :src="getPhotoURL(photo.Link)">
                                <p> {{ photo.Nom }} {{ photo.Vote }}</p>
                                <button class="btn btn-secondary" @click="deletePhoto(photo.id)">Supprimer</button>
                            </div>
                        </div>
                    </div>
                </div>
                <div>
                    <button class="btn btn-danger disable" @click="downloadCsv">Télécharger base de données des
                        Votants</button>
                </div>
            </div>
        </div>
    </div>
    <div>
        <button class="btn btn-primary" @click="showContest2 = !showContest2">Concours Type 2</button>
        <!-- Code à afficher lorsque le bouton est cliqué -->
        <div v-if="showContest2" class="collaps2">
            <form @submit.prevent="addDate" style="margin-bottom: 50px; background-color: rgba(128, 128, 128, 0.226);">
                <div class="form-group">
                    <h1>DATE DE DEBUT ET FIN DE L'ENVOI DES PHOTOS</h1>
                    <p v-for="date in dates" >Date du concours actuel <strong>{{ date.Datedebut }} </strong>du <strong>{{ date.Datedefin }}</strong></p>
                    <label for="Datedebut">Date de début</label>
                    <input type="date" id="Datedebut" v-model="newDate.Datedebut" class="form-control" required>
                </div>
                <div class="form-group">
                    <label for="Datedefin">Date de fin</label>
                    <input type="date" id="Datedefin" v-model="newDate.Datedefin" class="form-control" required>
                </div>
                <button type="submit" class="btn btn-success">Ajouter la date</button>
                <button type="button" class="btn btn-danger" @click="deleteAllDates">Supprimer toutes les dates</button>
            </form>
            <!-- Boucle sur les photo2s pour afficher chaque carte -->
            <div class="card" v-for="photo2 in photos2" :key="photo2.id" style="width: 200px;">
                <img :src="getPhotoURL(photo2.Photo)" class="card-img-top" alt="Photo2">
                <div class="card-body">
                    <h5 class="card-title">{{ photo2.Nom }} {{ photo2.Prenom }}</h5>
                    <p class="card-text">
                        <strong>Email:</strong> {{ photo2.Email }}<br>
                        <strong>Téléphone:</strong> {{ photo2.Telephone }}<br>
                        <strong>Vote:</strong> {{ photo2.Vote }}<br>
                        <strong>Catégorie ID:</strong>
                        <select @change="changeCategoryStatus(photo2)" v-model="photo2.Categorie_id">
                            <option v-for="categorie in categories" :key="categorie.id" :value="categorie.id">{{
                                categorie.Type }}</option>
                        </select>
                    </p>
                </div>
            </div>
            <div>
                <button class="btn btn-danger disable" @click="downloadCsv2">Télécharger base de données des
                    Votants</button>
            </div>
        </div>

    </div>
</template>


<script>

import axios from 'axios';
export default {
    data() {
        return {
            showContest1: false, // Initialement masqué
            showContest2: false,
            Prenom: "",
            Nom: "",
            Image: null,
            photos2: [],// Les données seront stockées ici,
            photos: [],
            dates: [],
            newDate: {
                Datedebut: "",
                Datedefin: ""
            },
            updateMessage: "", // Ajout de la variable pour le message de mise à jour
        };
    },
    mounted() {
        // Récupérez les catégories depuis l'API
        axios.get('https://studiophotov2-d849983bf69e.herokuapp.com/categorie')
            .then(response => {
                this.categories = response.data;
            })
            .catch(error => {
                console.error('Erreur lors de la récupération des catégories :', error);
            });
        axios.get('https://www.studioimageflask.com/photo')
            .then(response => {
                this.loading = false;
                this.photos = response.data;
                console.log(this.photos)
            })
            .catch(error => {
                console.error('Error:', error);
            });
        axios.get('http://127.0.0.1:5000/date')
            .then(response => {
                this.loading = false;
                this.dates = response.data;
                console.log(this.dates)
            })
            .catch(error => {
                console.error('Error:', error);
            });
        axios.get('https://studiophotov2-d849983bf69e.herokuapp.com/photo')
            .then(response => {
                this.loading = false;
                this.photos2 = response.data;
                console.log(this.photos2)
            })
            .catch(error => {
                console.error('Error:', error);
            });

    },
    methods: {
        deleteAllDates() {
    // Envoyez une requête à l'API pour supprimer toutes les dates
    axios.delete('http://127.0.0.1:5000/date')
      .then(response => {
        console.log(response.data);
        this.updateMessage = "Toutes les dates ont été supprimées avec succès";
        alert("Toutes les dates ont été supprimées avec succès");
        // Rechargez les données des dates si nécessaire
      })
      .catch(error => {
        console.error(error);
        this.updateMessage = "Erreur lors de la suppression de toutes les dates";
      });
  },
        addDate() {
            // Envoi de la nouvelle date à l'API
            axios.post('http://127.0.0.1:5000/date', this.newDate)
                .then(response => {
                    console.log(response.data);
                    alert("Date ajoutée avec succès");
                    this.updateMessage = "Date ajoutée avec succès";
                    this.newDate.Datedebut = "";
                    this.newDate.Datedefin = "";
                    // Rechargez les données des dates si nécessaire
                })
                .catch(error => {
                    console.error(error);
                    this.updateMessage = "Erreur lors de l'ajout de la date";
                });
        },
        changeCategoryStatus(photo2) {
            // Mettre à jour la catégorie de la photo dans la base de données
            axios.post(`https://studiophotov2-d849983bf69e.herokuapp.com/photo/update_categorie/${photo2.id}`, { nouvelle_categorie_id: photo2.Categorie_id })
                .then(response => {
                    this.updateMessage = "Catégorie mise à jour avec succès";
                })
                .catch(error => {
                    console.error(error);
                    this.updateMessage = "Erreur lors de la mise à jour de la catégorie";
                });
        },
        deletePhoto(photoId) {
            axios.delete(`https://www.studioimageflask.com/photo/delete/${photoId}`)

                .then(response => {
                    const index = this.photos.findIndex(photo => photo.id === photoId);
                    if (index !== -1) {
                        this.photos.splice(index, 1);
                    }
                    alert("La photo a été supprimée avec succès");
                })
                .catch(error => {
                    console.error(error);
                    alert("Une erreur s'est produite lors de la suppression de la photo");
                });
        },
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
            // Créer un objet FormData et ajouter le fichier sélectionné
            const file = event.target.files[0];
            const formData = new FormData();
            formData.append("Image", file);
            this.Image = formData;
        },
        downloadCsv() {
            axios.get('https://www.studioimageflask.com/download', { responseType: 'blob' })
                .then(response => {
                    const url = window.URL.createObjectURL(new Blob([response.data]))
                    const link = document.createElement('a')
                    link.href = url
                    link.setAttribute('download', 'admins.csv')
                    document.body.appendChild(link)
                    link.click()
                })
        },
        downloadCsv2() {
            axios.get('https://studiophotov2-d849983bf69e.herokuapp.com/download', { responseType: 'blob' })
                .then(response => {
                    const url = window.URL.createObjectURL(new Blob([response.data]))
                    const link = document.createElement('a')
                    link.href = url
                    link.setAttribute('download', 'admins.csv')
                    document.body.appendChild(link)
                    link.click()
                })
        },
        updateData() {
            const formData = new FormData();
            formData.append('Prenom', this.Prenom);
            formData.append('Nom', this.Nom);
            formData.append('Image', this.Image.get('Image'));
            axios.post("https://www.studioimageflask.com/photo", formData, {
                headers: {
                    "Content-Type": "multipart/form-data"
                }
            })
                .then(response => {
                    console.log(response.data);
                    const newPhoto = response.data;
                    newPhoto.imageUrl = this.getPhotoURL(newPhoto.Link);
                    this.photos.push(newPhoto);
                    alert("Merci de votre participation");
                })
                .catch(error => {
                    console.error(error);
                });
            this.updateMessage = "Donnée mise à jour"; // Ajout du message de mise à jour
            setTimeout(() => {
                location.reload();
            }, 1000);
        }
    },
};
</script>

<style scoped>
.collaps {
    display: flex;
    flex-direction: column;
    ;
    width: 70%;
    margin-left: auto;
    margin-right: auto;
}

.collaps2 {
    display: flex;
    flex-direction: column;
    ;
    width: 70%;
    margin-left: auto;
    margin-right: auto;
}

img {
    max-width: 200px;
    height: auto;
}

.pleft {
    margin-left: 5px;
}
</style>