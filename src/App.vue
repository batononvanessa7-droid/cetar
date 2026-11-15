<script setup>
import { ref } from 'vue'

const pageActive = ref('accueil')
const clients = ref([
  { id: 1, nom: 'MENSAH', prenom: 'Koffi', tel: '90 12 34 56', prof: 'Commerçant' }
])
const clientSelectionne = ref(null)

const affichFormClient = ref(false)
const affichFormDossier = ref(false)

const nouveauClient = ref({ nom: '', prenom: '', tel: '', prof: '' })
const nouveauDossier = ref({
  otrDepot: '', otrRecup: '', otrEtat: 'en_attente', otrNotes: '',
  tfNumero: '', tfInfos: '', tfDepot: '', tfRecup: '', tfNotes: ''
})

function ajouterClient() {
  if (!nouveauClient.value.nom || !nouveauClient.value.prenom) {
    alert('Veuillez renseigner au moins le nom et le prénom.')
    return
  }
  clients.value.push({
    id: Date.now(),
    nom: nouveauClient.value.nom.toUpperCase(),
    prenom: nouveauClient.value.prenom,
    tel: nouveauClient.value.tel || '—',
    prof: nouveauClient.value.prof || '—'
  })
  nouveauClient.value = { nom: '', prenom: '', tel: '', prof: '' }
  affichFormClient.value = false
}

function ouvrirFiche(client) {
  clientSelectionne.value = client
  pageActive.value = 'client'
}
</script>

<template>
  <div class="app-container">
    
    <header>
      <div class="header-content">
        <div class="logo-area" @click="pageActive = 'accueil'">
          <div class="logo-box">C</div>
          <div class="logo-title">
            <h1>CETAR</h1>
            <p>Cabinet de géométrie</p>
          </div>
        </div>
        <div class="header-right">Espace Secrétariat</div>
      </div>
    </header>

    <!-- PAGE 1 -->
    <main v-if="pageActive === 'accueil'">
      <div class="action-bar">
        <h2>Répertoire des clients</h2>
        <button @click="affichFormClient = !affichFormClient" class="btn-primary">
          <span style="font-size: 18px;">+</span>
          <span>Nouveau client</span>
        </button>
      </div>

      <div v-if="affichFormClient" class="form-card">
        <h3>Ajouter un nouveau client</h3>
        <div class="grid-4">
          <div class="form-group">
            <label>Nom</label>
            <input v-model="nouveauClient.nom" type="text" placeholder="Ex : Dupont">
          </div>
          <div class="form-group">
            <label>Prénom</label>
            <input v-model="nouveauClient.prenom" type="text" placeholder="Ex : Jean">
          </div>
          <div class="form-group">
            <label>Téléphone</label>
            <input v-model="nouveauClient.tel" type="text" placeholder="Ex : 06 12 34 56 78">
          </div>
          <div class="form-group">
            <label>Profession</label>
            <input v-model="nouveauClient.prof" type="text" placeholder="Ex : Architecte">
          </div>
        </div>
        <div class="form-actions">
          <button @click="affichFormClient = false" class="btn-secondary">Annuler</button>
          <button @click="ajouterClient" class="btn-primary">Enregistrer</button>
        </div>
      </div>

      <div class="table-container">
        <table>
          <thead>
            <tr>
              <th>Nom</th>
              <th>Prénom</th>
              <th>Téléphone</th>
              <th>Profession</th>
              <th style="text-align: right;">Dossiers</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="client in clients" :key="client.id">
              <td style="font-weight: 500;">{{ client.nom }}</td>
              <td>{{ client.prenom }}</td>
              <td>{{ client.tel }}</td>
              <td>{{ client.prof }}</td>
              <td style="text-align: right;">
                <button @click="ouvrirFiche(client)" class="btn-icon" title="Ouvrir les dossiers">
                  📂
                </button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </main>

    <!-- PAGE 2 -->
    <section v-if="pageActive === 'client'" class="section-container">
      <button @click="pageActive = 'accueil'" class="back-link">
        ← Retour à la liste des clients
      </button>

      <div class="client-header-card">
        <div>
          <span class="client-info-tag">Fiche personnelle client</span>
          <h2 class="client-name">{{ clientSelectionne?.prenom }} {{ clientSelectionne?.nom }}</h2>
          <div class="client-details">
            <span><strong>Tél :</strong> {{ clientSelectionne?.tel }}</span>
            <span><strong>Profession :</strong> {{ clientSelectionne?.prof }}</span>
          </div>
        </div>

        <button @click="affichFormDossier = !affichFormDossier" class="btn-primary">
          <span style="font-size: 18px;">+</span>
          <span>Nouveau dossier</span>
        </button>
      </div>

      <div v-if="affichFormDossier" class="form-card">
        <h3 style="border-bottom: 1px solid #f0f9ff; padding-bottom: 12px; margin-bottom: 20px;">Création / Suivi de dossier</h3>

        <div class="dossier-grid">
          <!-- Bloc OTR -->
          <div class="dossier-box">
            <div class="dossier-title">
              <div class="dot"></div> Partie OTR
            </div>
            <div class="space-y-4">
              <div class="form-group">
                <label>Date de dépôt</label>
                <input v-model="nouveauDossier.otrDepot" type="date">
              </div>
              <div class="form-group">
                <label>Date de récupération</label>
                <input v-model="nouveauDossier.otrRecup" type="date">
              </div>
              <div class="form-group">
                <label>État du dossier</label>
                <select v-model="nouveauDossier.otrEtat">
                  <option value="en_attente">En attente</option>
                  <option value="valide">Validé / Accepté</option>
                  <option value="rejete">Rejeté</option>
                </select>
              </div>
              <div class="form-group">
                <label>Notes</label>
                <textarea v-model="nouveauDossier.otrNotes" rows="3" placeholder="Observations..."></textarea>
              </div>
            </div>
          </div>

          <!-- Bloc Titre Foncier -->
          <div class="dossier-box">
            <div class="dossier-title">
              <div class="dot"></div> Partie Titre Foncier
            </div>
            <div class="space-y-4">
              <div class="form-group">
                <label>Numéro du dossier</label>
                <input v-model="nouveauDossier.tfNumero" type="text" placeholder="Ex : TF-2026-048">
              </div>
              <div class="form-group">
                <label>Informations</label>
                <input v-model="nouveauDossier.tfInfos" type="text" placeholder="Localisation, superficie...">
              </div>
              <div class="grid-2">
                <div class="form-group">
                  <label>Date dépôt</label>
                  <input v-model="nouveauDossier.tfDepot" type="date">
                </div>
                <div class="form-group">
                  <label>Date récup.</label>
                  <input v-model="nouveauDossier.tfRecup" type="date">
                </div>
              </div>
              <div class="form-group">
                <label>Notes</label>
                <textarea v-model="nouveauDossier.tfNotes" rows="3" placeholder="Remarques..."></textarea>
              </div>
            </div>
          </div>
        </div>

        <div class="form-actions" style="margin-top: 24px; border-top: 1px solid #f1f5f9; padding-top: 16px;">
          <button @click="affichFormDossier = false" class="btn-secondary">Annuler</button>
          <button @click="alert('Dossier enregistré !'); affichFormDossier = false" class="btn-primary">Enregistrer le dossier</button>
        </div>
      </div>
    </section>

  </div>
</template>