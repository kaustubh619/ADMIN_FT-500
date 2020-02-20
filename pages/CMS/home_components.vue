<template>
  <div class="navbar-spacing padding-top-30">
    <div class="specification">
      <div class="holder">
        <div class="row ml-3">
          <button class="update--btn" @click="saveHomeComponents">Update</button>
        </div>
        <div class="row">
          <div class="col-6">
            <h3>Active Components</h3>
            <draggable
              class="dragArea list-group"
              :list="list1"
              :clone="clone"
              :group="{ name: 'people', pull: pullFunction }"
              @start="start"
            >
              <div
                class="list-group-item"
                v-for="element in list1"
                :key="element.id"
              >{{ element.name }}</div>
            </draggable>
          </div>
          <div class="col-6">
            <h3>Inactive Components</h3>
            <draggable class="dragArea list-group" :list="list2" group="people">
              <div
                class="list-group-item"
                v-for="element in list2"
                :key="element.id"
              >{{ element.name }}</div>
            </draggable>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import draggable from "vuedraggable";
let idGlobal = 8;
export default {
  components: {
    draggable
  },
  data() {
    return {
      list1: [
        {
          name: "Kaustubh",
          id: 5
        },
        {
          name: "Abhishek",
          id: 6
        }
      ],
      list2: []
    };
  },
  mounted() {
    this.getInActiveComponents();
    this.getActiveComponents();
  },

  methods: {
    pullFunction() {
      return this.controlOnStart ? "clone" : true;
    },
    clone({ name }) {
      return { name, id: idGlobal++ };
    },
    start({ originalEvent }) {
      this.controlOnStart = originalEvent.ctrlKey;
    },

    getInActiveComponents: function() {
      this.$store.dispatch("getInActiveComponents").then(res => {
        this.list2 = JSON.parse(res.data.value);
      });
    },

    getActiveComponents: function() {
      this.$store.dispatch("getActiveComponents").then(res => {
        this.list1 = JSON.parse(res.data.value);
      });
    },

    saveHomeComponents: function() {
      const payload1 = new FormData();
      payload1.append("key", "Inactive Components");
      payload1.append("value", JSON.stringify(this.list2));
      this.$store.dispatch("updateInActiveComponents", payload1).then(res => {
        const payload2 = new FormData();
        payload2.append("key", "Active Components");
        payload2.append("value", JSON.stringify(this.list1));

        this.$store.dispatch("updateActiveComponents", payload2).then(res => {
          alert("Home Components Updated Successfully");
        });
      });
    }
  }
};
</script>

<style>
.list-group {
  min-height: 39px;
  padding-bottom: 1px;
  border: 1px dashed #d2d2d2;
}
.list-group-item {
  position: relative;
  display: block;
  padding: 0.75rem 1.25rem;
  background-color: #fff;
  border: 1px solid rgba(0, 0, 0, 0.125);
  cursor: move !important;
}
.handle {
  cursor: move !important;
}
.list-group-item i {
  cursor: pointer;
}
.sortable-chosen {
  background-color: #4caf50;
  color: white;
}

.update--btn {
  border: none;
  border-radius: 3px;
  padding: 4px 20px;
  font-size: 16px;
  background-color: #ffce10;
  color: black;
}

.update--btn:hover {
  background-color: #e4c443;
}

.update--btn:focus {
  outline: none;
}
</style>
