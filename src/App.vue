<template>
  <v-app>
    <v-content>
      <v-card flat>
        <v-toolbar color="primary" dark extended flat></v-toolbar>

        <v-card class="mx-auto" max-width="700" style="margin-top: -64px;">
          <v-toolbar flat>
            <v-btn icon>
              <v-icon>mdi-arrow-left</v-icon>
            </v-btn>
            <v-toolbar-title class="grey--text">Вакантные позиции</v-toolbar-title>

            <v-spacer></v-spacer>

            <v-btn @click="dialog = true" icon>
              <v-icon>mdi-plus</v-icon>
            </v-btn>
          </v-toolbar>

          <v-divider></v-divider>

          <v-card-text style="height: 100px;">
            <div class="d-flex justify-center flex-column align-center">
              <div class="mb-3 grey--text">Список вакантных позиций</div>
              <v-dialog v-model="dialog" persistent max-width="600px">
                <template v-slot:activator="{ on, attrs }">
                  <v-btn depressed color="primary" v-bind="attrs" v-on="on">добавить</v-btn>
                </template>
                <v-card>
                  <v-card-title>
                    <span class="headline">Новая позиция</span>
                  </v-card-title>
                  <v-card-text>
                    <v-container>
                      <v-row>
                        <v-col cols="12" sm="6" md="4">
                          <v-select v-model="form.lang" :items="languages" label="Язык" required></v-select>
                        </v-col>
                        <v-col cols="12" sm="6" md="4">
                          <v-select
                            v-model="form.prof"
                            :disabled="form.lang.length === 0"
                            :items="professions"
                            label="Позиция"
                            required
                          ></v-select>
                        </v-col>
                        <v-col cols="12" sm="6" md="4">
                          <v-select
                            v-model="form.tech"
                            :disabled="form.prof.length === 0"
                            :items="technologies"
                            label="Технология"
                            required
                          ></v-select>
                        </v-col>
                      </v-row>
                    </v-container>
                  </v-card-text>
                  <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="grey darken-1" text @click="clearForm">Отменить</v-btn>
                    <v-btn
                      color="green"
                      text
                      :disabled="form.prof.tech === 0"
                      @click="push"
                    >Добавить</v-btn>
                  </v-card-actions>
                </v-card>
              </v-dialog>
            </div>
          </v-card-text>
        </v-card>

        <v-card class="mx-auto mt-5" max-width="700" flat>
          <v-row>
            <v-col v-for="(pos, index) in positions" :key="index" cols="4">
              <v-card>
                <v-card-text>
                  <div>{{ pos.lang }}</div>
                  <div class="display-1 text--primary">{{ pos.prof }}</div>
                </v-card-text>
                <v-card-actions>
                  <v-spacer></v-spacer>

                  <v-btn @click="removePrepare(index)" color="red" icon>
                    <v-icon>mdi-delete</v-icon>
                  </v-btn>

                  <v-btn @click="edit(index)" icon>
                    <v-icon>mdi-pencil</v-icon>
                  </v-btn>
                </v-card-actions>
              </v-card>
            </v-col>
          </v-row>
        </v-card>
      </v-card>
    </v-content>

    <v-dialog v-model="deleteDialog" persistent max-width="290">
      <v-card>
        <v-card-title class="headline">Вы уверены?</v-card-title>
        <v-card-text>Вы подтверждаете удаление?</v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="red" text @click="remove">Удалить</v-btn>
          <v-btn text @click="deleteDialog = false">Отмена</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-app>
</template>

<style scoped></style>

<script>
export default {
  name: "App",

  components: {},

  data() {
    return {
      dialog: false,
      deleteDialog: false,
      deleteIndex: null,
      supportedProfs: {
        JavaScript: [
          {
            name: "Front-end developer",
            technologies: [
              {
                name: "Vue.js"
              },
              {
                name: "React"
              }
            ]
          },
          {
            name: "Back-end developer",
            technologies: [
              {
                name: "Node.js"
              }
            ]
          }
        ],
        Java: [
          {
            name: "Back-end developer",
            technologies: [
              {
                name: "Spring Framework"
              }
            ]
          }
        ]
      },
      form: {
        lang: "",
        prof: "",
        tech: ""
      },
      positions: []
    };
  },

  computed: {
    languages() {
      return Object.keys(this.supportedProfs);
    },
    professions() {
      const lang = this.form.lang;

      if (!lang) return [];

      const professions = this.supportedProfs[lang];

      return professions.map(p => p.name) || [];
    },
    technologies() {
      const lang = this.form.lang;
      const prof = this.form.prof;

      if (!(lang && prof)) return [];

      const professions = this.supportedProfs[lang];
      const technologies = professions
        .find(p => p.name === prof)
        .technologies.map(t => t.name);

      return technologies || [];
    }
  },
  methods: {
    edit(index) {
      this.form = this.positions[index];
      this.editMode = true;
      this.editIndex = index;
      this.dialog = true;
    },
    removePrepare(index) {
      this.deleteDialog = true;
      this.deleteIndex = index;
    },
    remove() {
      this.positions.splice(this.deleteIndex, 1);
      this.deleteDialog = false;
    },
    clearForm() {
      this.form.lang = "";
      this.form.prof = "";
      this.form.tech = "";
      this.dialog = false;
    },
    push() {
      if (!this.form.tech) return false;

      if (this.editMode) {
        const index = this.editIndex;
        this.positions[index] = { ...this.form };
      } else this.positions.push({ ...this.form });

      this.clearForm();
    }
  }
};
</script>
