<template>
  <v-dialog v-model="dialogVisible" max-width="500px">
    <v-card>
      <v-card-title style="text-align: center"> Add Teacher </v-card-title>
      <v-card-text>
        <v-text-field
          v-model="Teacher.teacherName"
          label="Name"
          variant="solo-filled"
          clearable
        ></v-text-field>
        <v-text-field
          v-model="Teacher.age"
          label="Age"
          type="number"
          variant="solo-filled"
          clearable
        ></v-text-field>
        <v-text-field
          v-model="Teacher.dateOfBirth"
          label="Dob (dddd/mmm/yyyy)"
          variant="solo-filled"
          clearabe
        ></v-text-field>
        <v-text-field
          v-model="Teacher.classesCount"
          label="Classes Count"
          type="number"
          variant="solo-filled"
          clearable
        ></v-text-field>
      </v-card-text>
      <v-card-actions>
        <v-btn color="blue darken-1" @click="addTeacher">Add</v-btn>
        <v-btn color="grey" @click="closeDialog">Cancel</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<script>
import axios from "axios";
export default {
  props: {
    value: Boolean,
  },
  data() {
    return {
      dialogVisible: false,
      Teacher: {
        teacherName: "",
        age: "",
        dateOfBirth: "",
        classesCount: "",
      },
    };
  },
  watch: {
    value(val) {
      this.dialogVisible = val;
    },
  },
  methods: {
    closeDialog() {
      this.dialogVisible = false;
      this.Teacher = {
        teacherName: "",
        age: "",
        dateOfBirth: "",
        classesCount: "",
      };
      this.$emit("handle-addDialog", false);
    },
    addTeacher() {
      if (
        !this.Teacher.teacherName ||
        !this.Teacher.age ||
        !this.Teacher.classesCount ||
        !this.Teacher.dateOfBirth
      ) {
        alert("Fill al the fields");
        return;
      }
      const url = "https://teachermanagementsystembackend.vercel.app/api/addTeacher";
      axios
        .post(url, this.Teacher)
        .then((response) => {
          if (response.status == 200) {
            this.$emit("success-Handler", true);
            this.$emit("handle-addDialog", false);
          }
        })
        .catch((error) => {
          console.log(error);
          alert("error in adding record");
        });

      this.closeDialog();
    },
  },
};
</script>
