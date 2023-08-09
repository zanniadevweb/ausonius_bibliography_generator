<template>
  <v-app>
    <v-app-bar>
      <v-container class="text-center">
        Générateur automatique de références bibliographiques selon la norme <a style="color: dodgerblue; text-decoration: none; font-weight: bold" href="https://ausoniuseditions.u-bordeaux-montaigne.fr/accueil" target="_blank">AUSONIUS ÉDITIONS</a>
      </v-container>
    </v-app-bar>

    <v-parallax src="bg.jpg">
      <v-main src="bg.jpg">

        <v-container>

          <v-responsive class="mx-auto">
            <v-card color="#4c5c96" class="v-card-border">

              <h2>Générateur automatique de références bibliographiques selon la norme <a style="color: dodgerblue; text-decoration: none; font-weight: bold" href="https://ausoniuseditions.u-bordeaux-montaigne.fr/accueil" target="_blank">AUSONIUS ÉDITIONS</a></h2>

              <v-divider class="my-12" />

              <v-container>
                <h2>1. Déterminer la typologie principale de l'ouvrage</h2>
                <v-container id="initialSection">
                  <div id="resultSingle" />
                  <div id="resultContainerWithInside" />
                  <h4>Type d'ouvrage : livre ou article ?</h4>
                  <v-select
                    id="inputBookOrArticle"
                    v-model="inputBookOrArticle"
                    class="text-black font-weight-black"
                    :items="['Livre', 'Article']"
                    @update:modelValue="hideIsContributionCheckBox($event)"
                  />
                  <span id="spanIsContributionCheckBox">
                    <v-row justify="space-between">
                      <v-col cols="auto">
                        <v-responsive width="350">
                          <h4>Il s'agit d'un document électronique ?</h4>
                        </v-responsive>
                      </v-col>
                      <v-checkbox
                        id="checkboxIsElectronicDocument"
                        v-model="checkboxIsElectronicDocument"
                        color="#1e90ff"
                        @change="hideElectronicDocumentInputs()"
                      />
                    </v-row>
                  </span>
                  <span v-show="selectIsBook" id="spanIsContributionCheckBox">
                    <v-row justify="space-between">
                      <v-col cols="auto">
                        <v-responsive width="350">
                          <h4>L'ouvrage doit en citer un autre ?</h4>
                        </v-responsive>
                      </v-col>
                      <v-checkbox
                        id="checkboxInsidePublication"
                        v-model="checkboxInsidePublication"
                        color="#1e90ff"
                        @update:modelValue="hideInsidePublicationCheckbox($event)"
                      />
                    </v-row>
                  </span>
                  <span v-if="selectIsBook" v-show="checkboxInsidePublication" id="spanWithShortCitationSelectMain">
                    <h4>Référence principale avec citation courte :</h4>
                    <v-select
                      id="inputShortCitationMain"
                      v-model="inputShortCitationMain"
                      class="text-black font-weight-black"
                      :items="['Oui', 'Non']"
                      @update:modelValue="hideShortCitationCheckbox($event)"
                    />
                  </span>
                </v-container>

                <v-divider class="my-12" />

                <v-container>
                  <h2>2. [Optionnel] 'Confirmer PPN' génère automatiquement</h2>
                  <v-container>
                    <div id="resultOuterContainerSudocApi" style="display: none;">
                      <div id="resultInnerContainerSudocApi">...</div>
                    </div>
                    <v-container>
                      <v-img src="ppn_sudoc.jpg" />
                    </v-container>
                    <v-row justify="space-between">
                      <v-col cols="auto">
                        <v-btn
                          prepend-icon="mdi-exit-to-app"
                          text="Ouvrir SUDOC"
                          href="http://www.sudoc.abes.fr/cbs/xslt/DB=2.1/SET=13/TTL=1/START_WELCOME.fr"
                          target="_blank"
                        />
                      </v-col>
                      <v-col cols="auto">
                        <v-responsive width="300">
                          <v-text-field
                            id="isInputPPNFilled"
                            placeholder="Ex. de valeur : 170583236"
                            class="text-black font-weight-black"
                            @update:modelValue="disableBtnConfirmPpn($event)"
                          />
                        </v-responsive>
                      </v-col>
                      <v-col cols="auto">
                        <v-btn
                          id="buttonConfirmPPN"
                          color="green"
                          :disabled="isInputPPNUnfilled"
                          @click="getXmlByPPN()"
                        >
                          CONFIRMER PPN
                        </v-btn>
                      </v-col>
                    </v-row>
                  </v-container>
                </v-container>

                <v-divider class="my-12" />

                <v-container id="resultSection">
                  <h2>3. [Optionnel] Remplir manuellement les champs</h2>

                  <v-divider class="my-12" />

                  <h2>4. 'Générer Référence' écrit la référence bibliographique</h2>
                  <v-container>
                    <v-btn
                      id="v-btnGenerateReference"
                      color="white"
                      variant="outlined"
                      class="font-weight-bold"
                      @click="buildReference()"
                    >
                      GÉNÉRER RÉFÉRENCE
                    </v-btn>
                  </v-container>
                  <v-divider class="my-12" />
                  <v-card color="white">
                    <v-container>
                      <v-row justify="space-between">
                        <h2>Résultat :</h2>
                        <v-btn
                          prepend-icon="mdi-content-copy"
                          text="Copier Résultat"
                          @click="copyTextFromInput"
                        />
                      </v-row>
                      <v-container>
                        <h3>
                          <input
                            id="spanResult"
                            style="font-family: 'Arial';
                            color:black;
                            font-size: 1em;
                            border: none;
                            outline: none;
                            width: 100%;
                            font-weight: normal"
                            value="..."
                            readonly
                          >
                        </h3>
                      </v-container>
                    </v-container>
                  </v-card>
                </v-container>

                <v-divider class="my-12" />

                <v-container id="mainSection">
                  <span id="spanSaisir"><h3>Référence principale :</h3></span>
                  <v-container>
                    <h4><label>Auteurs (Facultatif)</label></h4>
                    <v-combobox
                      id="inputAuthorsMain"
                      v-model="selectAuthorsMain"
                      placeholder="Ex. de valeur : Dupont, S., M."
                      class="text-black font-weight-black"
                      :items="itemsAuthorsMain"
                      label="Dupont, S., M."
                      multiple
                      chips
                      @update:modelValue="addAuthorSelectMain($event)"
                    />
                  </v-container>
                  <v-container>
                    Éd. ou Dir. ou Coll. ou Auteur :
                    <v-select
                      id="inputEdOrDirOrCollMain"
                      v-model="inputEdOrDirOrCollMain"
                      class="text-black font-weight-black"
                      :items="['éd.', 'dir.', 'coll.', 'Auteur']"
                    />
                    Date de première publication (Facultatif) :
                    <v-text-field
                      id="inputDateFirstMain"
                      v-model="inputDateFirstMain"
                      class="text-black font-weight-black"
                      placeholder="Ex. de valeur : 1995"
                    />
                    Date de publication (Facultatif) :
                    <v-text-field
                      id="inputDateMain"
                      v-model="inputDateMain"
                      class="text-black font-weight-black"
                      placeholder="Ex. de valeur : 2003"
                    />
                    Pages (Facultatif) :
                    <v-text-field
                      id="inputPagesMain"
                      v-model="inputPagesMain"
                      class="text-black font-weight-black"
                      placeholder="Ex. de valeur : 467-489"
                    />
                    <span v-show="!inputShortCitationBool" id="spanWithShortCitationUselessItems">
                      Collection / Revue (Facultatif) :
                      <v-text-field
                        id="inputEditorMain"
                        v-model="inputEditorMain"
                        class="text-black font-weight-black"
                        placeholder="Ex. de valeur : Gallia Suppl."
                      />
                      N° Collection / Tomaison (Facultatif) :
                      <v-text-field
                        id="inputNumberEditorMain"
                        v-model="inputNumberEditorMain"
                        class="text-black font-weight-black"
                        placeholder="Ex. de valeur : 59"
                      />
                      Titre d'ouvrage (Pour obtenir : saisir sup sup)
                      <v-text-field
                        id="inputTitleMain"
                        v-model="inputTitleMain"
                        class="text-black font-weight-black"
                        placeholder="Ex. de valeur : Plaidoyer pour la ‘petite épigraphie’&nbsp;: l’exemple de la cité de Béziers"
                      />
                      Lieu de publication (Facultatif) :
                      <v-text-field
                        id="inputLocationMain"
                        v-model="inputLocationMain"
                        class="text-black font-weight-black"
                        placeholder="Ex. de valeur : Paris"
                      />
                      <span v-show="selectIsBook" id="spanWithBookInputs">
                        Informations complémentaires édition (Facultatif) :
                        <v-text-field
                          id="inputBonusInfoEditionMain"
                          v-model="inputBonusInfoEditionMain"
                          class="text-black font-weight-black"
                          placeholder="Ex. de valeur : trad. fr. d’après la 2e éd. anglaise de 1957, 1re éd. Oxford"
                        />
                        N° Romain de Volume (Facultatif) :
                        <v-text-field
                          id="inputRomanNumberMain"
                          v-model="inputRomanNumberMain"
                          class="text-black font-weight-black"
                          placeholder="Ex. de valeur : v"
                        />
                        Genre (Facultatif) :
                        <v-text-field
                          id="inputGenreMain"
                          v-model="inputGenreMain"
                          class="text-black font-weight-black"
                          placeholder="rapport de fouille, thèse de doctorat..."
                        />
                        Établissement / Université (Facultatif) :
                        <v-text-field
                          id="inputUniversityMain"
                          v-model="inputUniversityMain"
                          class="text-black font-weight-black"
                          placeholder="Ex. de valeur : Université Bordeaux Montaigne"
                        />
                      </span>
                    </span>
                    <span v-show="checkboxIsElectronicDocument" id="spanWithElectronicDocumentInputs">
                      Date électronique de mise en ligne :
                      <v-text-field
                        id="inputDateElectronicUploadMain"
                        v-model="inputDateElectronicUploadMain"
                        class="text-black font-weight-black"
                        placeholder="Ex. de valeur : 06 février 2003"
                      />
                      Date électronique de consultation :
                      <v-text-field
                        id="inputDateElectronicConsultMain"
                        v-model="inputDateElectronicConsultMain"
                        class="text-black font-weight-black"
                        placeholder="Ex. de valeur : 30 décembre 2022"
                      />
                      Type ressource électronique :
                      <v-select
                        id="inputDoiOrUrlMain"
                        v-model="inputDoiOrUrlMain"
                        class="text-black font-weight-black"
                        :items="['DOI', 'URL']"
                        @update:modelValue="changeTextInputUrlMain()"
                      />
                      <v-text-field
                        id="inputUrlMain"
                        v-model="inputUrlMain"
                        class="text-black font-weight-black"
                        placeholder="Ex. de valeur : 10.1016/S8756-3282(01)00704-9"
                      />
                    </span>
                  </v-container>
                </v-container>

                <v-divider class="my-12" />

                <v-container v-if="selectIsBook" v-show="checkboxInsidePublication" id="secondarySection">
                  <span
                    id="spanWithInsidePublicationInputs"
                  >
                    <span id="spanSaisir"><h3>Référence contenue dans la principale :</h3></span>
                    <v-container>
                      <h4><label>Auteurs (Facultatif)</label></h4>
                      <v-combobox
                        id="inputAuthorsInside"
                        v-model="selectAuthorsInside"
                        placeholder="Ex. de valeur : Dupont, S., M."
                        class="text-black font-weight-black"
                        :items="itemsAuthorsInside"
                        label="Dupont, S., M."
                        multiple
                        chips
                        @update:modelValue="addAuthorSelectInside($event)"
                      />
                    </v-container>

                    <v-container>
                      Éd. ou Dir. ou Coll. ou Auteur :
                      <v-select
                        id="inputEdOrDirOrCollInside"
                        v-model="inputEdOrDirOrCollInside"
                        class="text-black font-weight-black"
                        :items="['éd.', 'dir.', 'coll.', 'Auteur']"
                      />
                      Date de publication (Facultatif) :
                      <v-text-field
                        id="inputDateInside"
                        v-model="inputDateInside"
                        class="text-black font-weight-black"
                        placeholder="Ex. de valeur : 2003"
                      />
                      Date de première publication (Facultatif) :
                      <v-text-field
                        id="inputDateFirstInside"
                        v-model="inputDateFirstInside"
                        class="text-black font-weight-black"
                        placeholder="Ex. de valeur : 2003"
                      />
                      Titre d'ouvrage (Pour obtenir : saisir sup sup)
                      <v-text-field
                        id="inputTitleInside"
                        v-model="inputTitleInside"
                        class="text-black font-weight-black"
                        placeholder="Ex. de valeur : Plaidoyer pour la ‘petite épigraphie’&nbsp;: l’exemple de la cité de Béziers"
                      />
                    </v-container>
                  </span>
                </v-container>

                <v-divider class="my-12" />

              </v-container>

            </v-card>
          </v-responsive>

        </v-container>

      </v-main>
    </v-parallax>

    <v-footer>
      <v-container class="text-center">
          <v-container>
            Mise à jour du 02/03/2023 : Ajout fonctionnalité interrogation du
            SUDOC via un n°PPN d'ouvrage pour pré-remplir les champs. Aucune case
            ne peut être cochée en ce but. Seuls les livres sont concernés par cette fonctionnalité.
          <v-container>
          <li>Développeur :
            <a
              style="color: dodgerblue; text-decoration: none; font-weight: bold"
              href="https://ausonius.u-bordeaux-montaigne.fr/annuaire?chercheur=346"
              target="_blank"
            >
              Alexandre Zanni (Doctorant Archéologie, UMR 5607 Ausonius)
            </a>
          </li>
          <li>Consultant et testeur : Julien Gravier (Doctorant Archéologie, UMR 5607 Ausonius)</li>
        </v-container>
        </v-container>
        <v-container>
          Copyright &copy; {{ (new Date()).getFullYear() }} Alexandre ZANNI
        </v-container>
      </v-container>
    </v-footer>
  </v-app>
</template>

<script>
  import { defineComponent } from 'vue'

  // https://vuetifyjs.com/en/components/combobox/
  // https://pictogrammers.github.io/@mdi/font/1.1.34/

  export default defineComponent({
    data () {
      return {
        inputBookOrArticle: 'Livre',
        selectIsBook: true,
        checkboxIsElectronicDocument: false,
        checkboxInsidePublication: false,
        inputShortCitationMain: 'Non',
        inputShortCitationBool: false,
        isInputPPNUnfilled: true,
        bibliographicReferenceInsidePublication: [],
        selectAuthorsInside: [],
        itemsAuthorsInside: [],
        inputEdOrDirOrCollInside: 'éd.',
        inputDateInside: '',
        inputDateFirstInside: '',
        inputTitleInside: '',
        bibliographicReferenceMainPublication: [],
        selectAuthorsMain: [],
        itemsAuthorsMain: [],
        inputEdOrDirOrCollMain: 'éd.',
        inputDateFirstMain: '',
        inputDateMain: '',
        inputPagesMain: '',
        inputEditorMain: '',
        inputNumberEditorMain: '',
        inputTitleMain: '',
        inputLocationMain: '',
        inputBonusInfoEditionMain: '',
        inputRomanNumberMain: '',
        inputGenreMain: '',
        inputUniversityMain: '',
        inputDateElectronicUploadMain: '',
        inputDateElectronicConsultMain: '',
        inputDoiOrUrlMain: '',
        inputUrlMain: '',
      }
    },
    mounted () {
    },
    methods: {
      addAuthorSelectMain (event) {
        this.itemsAuthorsMain = this.selectAuthorsMain
      },
      addAuthorSelectInside (event) {
        this.itemsAuthorsInside = this.selectAuthorsInside
      },
      buildReference () {
        if (Array.isArray(this.selectAuthorsMain) && this.selectAuthorsMain.length > 0) {
          this.selectAuthorsMain = this.addAuthors(this.selectAuthorsMain)
        }
        if (Array.isArray(this.selectAuthorsInside) && this.selectAuthorsInside.length > 0) {
          this.selectAuthorsInside = this.addAuthors(this.selectAuthorsInside)
        }
        this.bibliographicReferenceInsidePublication = this.createInsidePublication()
        this.bibliographicReferenceMainPublication = this.createMainPublication()

        let wholeBibliographicReference = ''
        if (this.selectIsBook && this.checkboxInsidePublication) {
          wholeBibliographicReference = this.bibliographicReferenceInsidePublication +
            ' in : ' +
            this.bibliographicReferenceMainPublication
        } else {
          wholeBibliographicReference = this.bibliographicReferenceMainPublication
        }

        document.getElementById('spanResult').value = wholeBibliographicReference
      },
      copyTextFromInput () {
        const copyText = document.getElementById('spanResult')
        copyText.select()
        copyText.setSelectionRange(0, 99999) // For mobile devices
        document.execCommand('copy')
        alert('Copied the text: ' + copyText.value)
      },
      hideIsContributionCheckBox (event) {
        if (event === 'Livre') {
          this.selectIsBook = true
        } else {
          this.inputShortCitationMain = 'Non'
          this.inputShortCitationBool = false
          this.selectIsBook = false
        }
      },
      hideShortCitationCheckbox (event) {
        if (event === 'Oui') {
          this.inputShortCitationBool = true
        } else {
          this.inputShortCitationBool = false
        }
      },
      hideInsidePublicationCheckbox (event) {
        this.inputShortCitationMain = 'Oui'
        this.inputShortCitationBool = true
        if (!event) {
          this.inputShortCitationMain = 'Non'
          this.inputShortCitationBool = false
        }
      },
      disableBtnConfirmPpn (event) {
        if (event !== undefined && event.length > 0) {
          this.isInputPPNUnfilled = false
        } else {
          this.isInputPPNUnfilled = true
        }
      },
      addAuthors (arrayOfAuthors, insidePublication, isInsidePublicationAuthors = false) {
        let facultativeLinkingWord = ''
        const inputShortCitationMain = document.getElementById('inputShortCitationMain').value
        let elementsExceptLastOneArray = arrayOfAuthors.slice(0, -1).join(', ')
        let lastElementArray = arrayOfAuthors.slice(-1)

        if (arrayOfAuthors.length > 1) {
          if (inputShortCitationMain === 0 && insidePublication.length !== 0 && !isInsidePublicationAuthors) { // CITATION COURTE
            if (arrayOfAuthors.length === 2) {
              facultativeLinkingWord = ' & '
            } else {
              elementsExceptLastOneArray = arrayOfAuthors[0]
              lastElementArray = ''
              facultativeLinkingWord = (' et al.').italics()
            }
          } else { // CITATION LONGUE
            facultativeLinkingWord = ' et '
          }
        } else {
          facultativeLinkingWord = ''
        }

        return elementsExceptLastOneArray + facultativeLinkingWord + lastElementArray
      },
      addCommaBetweenExceptLastOne (arrayOfElements) {
        return arrayOfElements.join(', ')
      },
      createMainPublication () {
        const arr = [this.selectAuthorsMain,
                     this.inputEdOrDirOrCollMain,
                     '[' + this.inputDateFirstMain + ']',
                     '(' + this.inputDateMain + ')',
                     this.inputPagesMain,
                     this.inputEditorMain,
                     this.inputNumberEditorMain,
                     this.inputTitleMain,
                     this.inputLocationMain,
                     this.inputBonusInfoEditionMain,
                     this.inputRomanNumberMain,
                     this.inputGenreMain,
                     this.inputUniversityMain,
                     ' [En ligne], mis en ligne le ' + this.inputDateElectronicUploadMain,
                     'consulté le ' + this.inputDateElectronicConsultMain,
                     ' :  ' + this.inputDoiOrUrlMain,
                     this.inputUrlMain]

        const str = arr.join(', ') + '.'

        return str
      },
      createInsidePublication () {
        const arr = [this.selectAuthorsInside,
                     this.inputEdOrDirOrCollInside,
                     this.inputDateInside,
                     this.inputDateFirstInside,
                     this.inputTitleInside]

        const str = arr.join(', ')

        return str
      },
    },
  })
</script>

<style>
  * {
    font-family: 'Arial';
  }
  .rounded-card{
    border-radius:50px;
  }
  .v-field__input{
    background: white;
  }
  .v-field{
    background: #1E90FF;
  }
  .v-card-border{
    border-top: 75px solid #4c5c96 !important;
    border-left: 75px solid #4c5c96 !important;
    border-right: 75px solid #4c5c96 !important;
    border-bottom: 75px solid #4c5c96 !important;
  }
  .stroke-text-black {
    -webkit-text-stroke-width: 1px;
    -webkit-text-stroke-color: black;
  }
  .stroke-text-white {
    -webkit-text-stroke-width: 0.1px;
    -webkit-text-stroke-color: white;
  }
  .v-btn-plain > .v-btn__underlay {
    opacity: 1;
  }
</style>
