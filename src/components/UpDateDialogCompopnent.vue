<template>
  <v-dialog v-model="dialogVisible" max-width="500px">
    <v-card>
      <v-card-title style="text-align: center"> Update Teacher </v-card-title>
      <v-card-text>
        <v-text-field
          v-model="editedTeacher.teacherName"
          label="Name"
          variant="solo-filled"
          clearable
        ></v-text-field>
        <v-text-field
          v-model="editedTeacher.age"
          label="Age"
          variant="solo-filled"
          type="number"
          clearable
        ></v-text-field>
        <v-text-field
          v-model="editedTeacher.dateOfBirth"
          label="Dob (dddd/mmm/yyyy)"
          variant="solo-filled"
          clearable
        ></v-text-field>
        <v-text-field
          v-model="editedTeacher.classesCount"
          label="Classes Count"
          type="number"
          variant="solo-filled"
          clearable
        ></v-text-field>
      </v-card-text>
      <v-card-actions>
        <v-btn color="blue darken-1" @click="updateTeacher">Update</v-btn>
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
    teacher: Object,
  },
  data() {
    return {
      dialogVisible: false,
      editedTeacher: {
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
      this.setData(this.$props.teacher);
    },
  },
  methods: {
    closeDialog() {
      this.dialogVisible = false;
      this.editedTeacher = {
        teacherName: "",
        age: "",
        dateOfBirth: "",
        classesCount: "",
      };
      this.$emit("handle-updateDialog", false);
    },
    setData(oldData) {
      console.log(oldData);
      this.editedTeacher.teacherName = oldData.name;
      this.editedTeacher.age = oldData.age;
      this.editedTeacher.dateOfBirth = oldData.dob;
      this.editedTeacher.classesCount = oldData.classesCount;
    },
    updateTeacher() {
      if (
        !this.editedTeacher.teacherName ||
        !this.editedTeacher.age ||
        !this.editedTeacher.classesCount ||
        !this.editedTeacher.dateOfBirth
      ) {
        alert("Fill al the fields");
        return;
      }
      axios
        .put(
          `https://teachermanagementsystembackend.vercel.app/api/updateTeacher/${this.$props.teacher.id}`,
          this.editedTeacher
        )
        .then((response) => {
          if (response.status == 200) {
            this.$emit("success-Handler", true);
            this.$emit("handle-updateDialog", false);
          }
        })
        .catch((error) => {
          console.log(error);
          alert("error in updating record");
        });
      this.closeDialog();
    },
  },
};
</script>
