<template>
  <v-app class="poppins-font">
    <nav>
      <h1 class="nav-title poppins-font">Teacher Management Portal</h1>
    </nav>
    <v-main>
      <div class="menu-container">
        <div style="width: 30%">
          <v-text-field
            v-model="search"
            prepend-inner-icon="mdi-magnify"
            label="Search by name"
            density="compact"
            single-line
            flat
            clearable
            hide-details
            variant="solo-filled"
          ></v-text-field>
        </div>
        <div style="width: 20%">
          <v-btn
            prepend-icon="mdi-plus"
            variant="flat"
            color="#5865f2"
            style="padding: 0rem 1rem"
            @click="addTeacher()"
          >
            Add Teacher
          </v-btn>
        </div>

        <div class="filter-container">
          <v-text-field
            v-model="filterClassCount"
            prepend-inner-icon="mdi-filter"
            label="Filter by classes count"
            type="number"
            density="compact"
            single-line
            flat
            clearable
            hide-details
            variant="solo-filled"
          >
          </v-text-field>
          <v-btn
            variant="flat"
            color="green"
            style="padding: 0rem 1rem"
            @click="filterbyClassesCount(filterClassCount)"
          >
            Filter
          </v-btn>
        </div>
        <div class="filter-container">
          <v-text-field
            v-model="filterAge"
            prepend-inner-icon="mdi-filter"
            label="Filter by age"
            type="number"
            density="compact"
            single-line
            flat
            clearable
            hide-details
            variant="solo-filled"
          >
          </v-text-field>
          <v-btn
            variant="flat"
            color="green"
            style="padding: 0rem 1rem"
            @click="filterbyAge(filterAge)"
          >
            Filter
          </v-btn>
        </div>
      </div>
      <v-data-table
        :headers="headers"
        :items="teachers"
        item-key="id"
        :search="search"
      >
        <template v-slot:thead>
          <thead style="background-color: orange">
            <tr>
              <th v-for="(header, index) in headers" :key="index">
                {{ header.text }}
              </th>
            </tr>
          </thead>
        </template>
        <template v-slot:item="{ item }">
          <tr>
            <td>{{ item.name }}</td>
            <td>{{ item.age }}</td>
            <td>{{ item.dob }}</td>
            <td>{{ item.classesCount }}</td>
            <td>
              <v-btn
                @click="updateTeacher(item)"
                style="padding: 0rem 1rem"
                color="primary"
                >Update</v-btn
              >
            </td>
            <td>
              <v-btn
                @click="deleteTeacher(item)"
                color="error"
                style="padding: 0rem 1rem"
                >Delete</v-btn
              >
            </td>
          </tr>
        </template>
      </v-data-table>
      <h3 style="text-align: center; margin-top: 2rem">
        Average number of classes teacher takes :
        <span> {{ averageClassTake }}</span>
      </h3>
    </v-main>
    <UpDateDialogCompopnentVue
      :value="updateDialog"
      :teacher="teacherdata"
      @success-Handler="handleUpdate"
      @handle-updateDialog="handleUpdateDialog"
    ></UpDateDialogCompopnentVue>
    <AddDialogComponentVue
      :value="addDialog"
      @success-Handler="handleAdd"
      @handle-addDialog="handleAddDialog"
    >
    </AddDialogComponentVue>
  </v-app>
</template>

<script>
import UpDateDialogCompopnentVue from "./components/UpDateDialogCompopnent.vue";
import AddDialogComponentVue from "./components/AddDialogComponent.vue";
import axios from "axios";
export default {
  name: "App",
  components: {
    UpDateDialogCompopnentVue,
    AddDialogComponentVue,
  },
  data() {
    return {
      search: "",
      headers: [
        { text: "Name", value: "name" },
        { text: "Age", value: "age" },
        { text: "Date of Birth", value: "dob" },
        { text: "Classes Count", value: "classesCount" },
        { text: "Update", value: "update" },
        { text: "Delete", value: "delete" },
      ],
      teachers: [],
      updateDialog: false,
      addDialog: false,
      filterClassCount: "",
      filterAge: "",
      averageClassTake: 0,
      teacherdata: {
        name: "",
        age: "",
        dob: "",
        classesCount: "",
      },
    };
  },
  created() {
    this.viewTeacherRecords();
  },
  watch: {
    filterClassCount(val) {
      if (!val) {
        this.teachers = [];
        this.viewTeacherRecords();
      }
    },
    filterAge(val) {
      if (!val) {
        this.teachers = [];
        this.viewTeacherRecords();
      }
    },
  },
  methods: {
    viewTeacherRecords() {
      axios
        .get("https://teachermanagementsystembackend.vercel.app/api/getAllTeachers")
        .then((response) => {
          this.setData(response.data.data);
        })
        .catch((error) => {
          console.log(error);
        });
    },
    setData(teacherData) {
      for (let data of teacherData) {
        let newData = {
          id: data._id,
          name: data.teacherName,
          age: data.age,
          dob: data.dateOfBirth,
          classesCount: data.classesCount,
        };
        this.teachers.push(newData);
      }
      this.findAverageClassTake(this.teachers);
    },
    updateTeacher(teacher) {
      this.teacherdata.name = teacher.name;
      this.teacherdata.age = teacher.age;
      this.teacherdata.dob = teacher.dob;
      this.teacherdata.classesCount = teacher.classesCount;
      this.teacherdata.id = teacher.id;
      this.updateDialog = true;
    },
    addTeacher() {
      this.addDialog = true;
    },
    deleteTeacher(teacher) {
      axios
        .delete(
          "https://teachermanagementsystembackend.vercel.app/api/deleteTeacher/" + teacher.id
        )
        .then((response) => {
          if (response.status == 200) {
            this.handleDelete();
          }
        })
        .catch((error) => {
          console.log(error);
          alert("error in deleting record");
        });
    },
    handleAdd(data) {
      if (data) {
        this.teachers = [];
        this.viewTeacherRecords();
        alert("Records added successfully");
      }
    },
    handleUpdate(data) {
      if (data) {
        this.teachers = [];
        this.viewTeacherRecords();
        alert("Record updated successfully");
      }
    },
    handleDelete() {
      this.teachers = [];
      this.viewTeacherRecords();
      alert("record deleted successfully");
    },
    handleUpdateDialog(data) {
      this.updateDialog = data;
    },
    handleAddDialog(data) {
      this.addDialog = data;
    },
    filterbyClassesCount(value) {
      this.teachers = this.teachers.filter(
        (data) => data.classesCount == value
      );
    },
    filterbyAge(value) {
      this.teachers = this.teachers.filter((data) => data.age == value);
    },
    findAverageClassTake(teacherArray) {
      if (teacherArray.length > 0) {
        let TotalClassTaken = 0;
        for (let data of teacherArray) {
          TotalClassTaken = TotalClassTaken + data.classesCount;
        }
        this.averageClassTake = TotalClassTaken / teacherArray.length;
      }
    },
  },
};
</script>

<style scoped>
@import url("https://fonts.googleapis.com/css2?family=Poppins&display=swap");
.nav-title {
  font-size: 30px;
  font-weight: 600;
  color: #bd0b0b;
  text-align: center;
  margin-bottom: 2rem;
  margin-top: 1rem;
}
* {
  margin: 0;
  padding: 0;
}

.filter-container {
  display: flex;
  gap: 1rem;
  width: 30%;
}
.menu-container {
  display: flex;
  align-items: center;
  gap: 3rem;
  margin: 2rem;
}
.poppins-font {
  font-family: "Poppins", sans-serif;
}

.search-bar {
  width: 20%;
}
</style>
