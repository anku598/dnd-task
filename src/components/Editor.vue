<template>
  <div>
    <button @click="editorOpen = !editorOpen" class="editor-btn">
      {{ editorOpen ? "Close " : "Open Editor" }}
    </button>
    <div class="editor-drawer" :class="{ open: editorOpen }">
      <div class="toolbar">
        <button @click="addText">Add Text</button>
        <button @click="addField">Add Field</button>
        <button @click="addButton">Add Button</button>
        <button @click="addSmalltext">Add Small Text</button>
        <button @click="addStars">Add Stars</button>
      </div>

      <div class="color-picker">
        <label for="bg-color">Background color</label>
        <input type="color" id="bg-color" v-model="bgColor" />
      </div>
      <div class="color-picker">
        <label for="text-color">Text color</label>
        <input type="color" id="text-color" v-model="textColor" />
      </div>
      <div class="change-action-btn">
        <button @click="saveChanges">Save</button>
        <button @click="previewChanges">Preview</button>
      </div>
    </div>

    <div
      class="pop-up-wrapper"
      :style="{ backgroundColor: bgColor, color: textColor }"
    >
      <div class="nested-content-wrapper">
        <Container @drop="onDrop">
          <Draggable v-for="(item, index) in items" :key="item.id">
            <div :class="{ active: activeIndex === index }">
              <div class="star-rating" v-if="item.type === 'stars'">
                <i class="fa-sharp fa-solid fa-star"></i>
                <i class="fa-sharp fa-solid fa-star big"></i>
                <i class="fa-sharp fa-solid fa-star"></i>

                <i
                  @click="deleteItem(index)"
                  v-if="editorOpen"
                  class="fa-solid fa-trash delete"
                ></i>
              </div>
              <div
                class="text-element"
                v-if="item.type === 'text'"
                :contenteditable="true"
                @input="updateText(item, $event.target.innerText)"
              >
                <p :style="{ color: textColor }">
                  {{ item.text }}
                </p>

                <i
                  @click="deleteItem(index)"
                  v-if="editorOpen"
                  class="fa-solid fa-trash delete"
                ></i>
              </div>

              <div
                class="input-field"
                v-if="item.type === 'field'"
                :type="item.fieldType"
              >
                <input
                  :placeholder="item.placeholder"
                  @input="updateField(item, $event.target.value)"
                  type="text"
                  :contenteditable="true"
                />

                <i
                  @click="deleteItem(index)"
                  v-if="editorOpen"
                  class="fa-solid fa-trash delete"
                ></i>
              </div>

              <div
                class="submit-btn"
                :contenteditable="true"
                v-if="item.type === 'button'"
              >
                <button :contenteditable="true" :style="{ color: textColor }">
                  {{ item.btntext }}
                </button>

                <i
                  v-if="editorOpen"
                  @click="deleteItem(index)"
                  class="fa-solid fa-trash delete"
                ></i>
              </div>

              <div
                class="smaller-info-text"
                v-if="item.type === 'smalltext'"
                :style="{ color: textColor }"
                :contenteditable="true"
              >
                {{ item.text }}

                <i
                  v-if="editorOpen"
                  @click="deleteItem(index)"
                  class="fa-solid fa-trash delete"
                ></i>
              </div>
            </div>
          </Draggable>
        </Container>
      </div>
    </div>

    <div class="preview-text" v-if="previewMode">
      <h4>You are in Preview Mode ! Reload to see previous Version</h4>
    </div>
  </div>
</template>

<script>
import { Container, Draggable } from "vue-smooth-dnd";
import { applyDrag } from "../utils/helper.js";

export default {
  name: "Editor",
  components: { Container, Draggable },
  data() {
    return {
      items: [],
      activeIndex: null,
      editorOpen: false,
      bgColor: "#e07a5f",
      textColor: "#ffffff",
      previewMode: false,
    };
  },
  methods: {
    addText() {
      this.items.push({
        id: this.items.length + 1,
        type: "text",
        text: "All the Text and elements in this popup should be editable and dragabble",
      });
    },

    addField() {
      this.items.push({
        id: this.items.length + 1,
        type: "field",
        fieldType: "text",
        placeholder: "Input Field",
        value: "",
      });
    },

    addButton() {
      this.items.push({
        id: this.items.length + 1,
        type: "button",
        btntext: "Sign Up",
      });
    },

    addSmalltext() {
      this.items.push({
        id: this.items.length + 1,
        type: "smalltext",
        text: "No Credit card required. No Surprises",
      });
    },

    addStars() {
      this.items.push({
        id: this.items.length + 1,
        type: "stars",
        // starList: "No Credit card required. No Surprises",
      });
    },

    updateText(item, text) {
      item.text = text;
      this.save();
    },
    updateField(item, value) {
      item.value = value;
      this.save();
    },
    save() {
      localStorage.setItem("items", JSON.stringify(this.items));
      localStorage.setItem("bgColor", this.bgColor);
      localStorage.setItem("textColor", this.textColor);
    },

    saveChanges() {
      localStorage.setItem("items", JSON.stringify(this.items));
      localStorage.setItem("bgColor", this.bgColor);
      localStorage.setItem("textColor", this.textColor);
      this.editorOpen = false;
      this.previewMode = false;
    },

    previewChanges() {
      localStorage.setItem("preview", JSON.stringify(this.items));
      this.editorOpen = false;
      this.previewMode = true;
    },

    onDrop(dropResult) {
      this.items = applyDrag(this.items, dropResult);
      this.save();
    },

    deleteItem(index) {
      this.items.splice(index, 1);
      this.save();
    },
  },
  mounted() {
    const savedItems = localStorage.getItem("items");
    if (savedItems) {
      this.items = JSON.parse(savedItems);
    } else {
      this.items = [
        {
          id: 1,
          type: "stars",
          //   starlist: "All the Text and elements in this popup should be editable and dragabble",
        },
        {
          id: 2,
          type: "text",
          text: "All the Text and elements in this popup should be editable and dragabble",
        },
        {
          id: 3,
          type: "field",
          fieldType: "text",
          placeholder: "Email",
          value: "",
        },
        {
          id: 4,
          type: "button",

          btntext: "Sign Up",
        },
        {
          id: 5,
          type: "smalltext",
          text: "No Credit card required. No Surprises",
        },

        // add more initial items as needed
      ];
      this.save();
    }

    this.items = [
      {
        id: 1,
        type: "stars",
        //   starlist: "All the Text and elements in this popup should be editable and dragabble",
      },
      {
        id: 2,
        type: "text",
        text: "All the Text and elements in this popup should be editable and dragabble",
      },
      {
        id: 3,
        type: "field",
        fieldType: "text",
        placeholder: "Email",
        value: "",
      },
      {
        id: 4,
        type: "button",

        btntext: "Sign Up",
      },
      {
        id: 5,
        type: "smalltext",
        text: "No Credit card required. No Surprises",
      },

      // add more initial items as needed
    ];

    const savedBgColor = localStorage.getItem("bgColor");
    if (savedBgColor) {
      this.bgColor = savedBgColor;
    }

    const savedTextColor = localStorage.getItem("textColor");
    if (savedTextColor) {
      this.textColor = savedTextColor;
    }
    this.save();
  },
};
</script>

<style scoped>
.pop-up-wrapper {
  width: 500px;
  height: 500px;
  border-radius: 100%;
  background: #e07a5f;
  margin: 0 auto;
  margin-top: 25px;
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  opacity: 0;
  animation: fadeIn 0.5s ease-out forwards;
}

.pop-up-wrapper::before {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 470px;
  height: 470px;
  border-radius: 100%;
  border: 2px solid #fff;
  content: "";
}

.nested-content-wrapper {
  max-width: 70%;
  width: 100%;
  text-align: center;
  position: relative;
  z-index: 25;
}

.star-rating {
  position: absolute;
  top: -50px;
  width: 100%;
  text-align: center;
  display: flex;
  justify-content: center;
  align-items: center;
}

.star-rating i {
  font-size: 25px;
  color: #c75943;
  margin-right: 18px;
}

.star-rating i:last-child {
  margin-right: 0px;
}

.star-rating .big {
  font-size: 30px;
  transform: translateY(-10px);
}

.text-element {
  margin-bottom: 20px;
  position: relative;
}

.text-element p {
  font-size: 20px;
  color: #fff;
  font-weight: 600;
}

.input-field {
  position: relative;
}

.input-field input {
  width: 100%;
  background: #fff;
  border-radius: 10px;
  height: 40px;
  box-shadow: none;
  border: none;
  outline: 0;
  padding: 25px 15px;
  margin-bottom: 20px;
  font-size: 18px;
  color: #949698;
}

.input-field input::placeholder {
  color: #949698;
  font-size: 18px;
  text-transform: capitalize;
  line-height: 5px;
}

.submit-btn {
  margin-bottom: 20px;
  position: relative;
  background: #414142;
  width: 100%;
  padding: 15px 10px;
  border-radius: 10px;
  font-weight: 700;
}

.submit-btn button {
  text-transform: uppercase;
  border: none;
  color: #fff;
  font-size: 18px;
  cursor: pointer;
  background: transparent;
  border: none;
  outline: none;
  width: 100%;
}

.smaller-info-text {
  color: #fff;
  font-size: 15px;
  position: relative;
}

.delete {
  position: absolute;
  top: 0;
  right: 0;
  color: black;
}

.editor-drawer.open {
  transform: translateX(0);
}

.editor-drawer {
  position: fixed;
  right: 0px;
  background: #020202;
  max-width: 300px;
  width: 100%;
  /* min-height: 50vh; */
  top: 160px;
  transform: translateX(100%);
  transition: transform 0.3s ease-in-out;
}

.toolbar {
  display: flex;
  flex-direction: column;
  /* height: 100%; */
}

.toolbar button,
.change-action-btn button {
  background: transparent;
  border-bottom: 1px solid #fff;
  padding: 15px 20px;
  color: #fff;
  cursor: pointer;
  font-size: 17px;
  text-transform: capitalize;
  font-weight: 600;
  opacity: 1;
  transition: opacity 0.3s ease-in-out;
  text-align: left;
}
.toolbar button:hover {
  opacity: 0.5;
}

.change-action-btn button {
  background: #2b2b2b;
  flex: 1;
  text-align: center;
  margin: 0;
  border: 0;
  border-right: 1px solid #fff;
}

.change-action-btn button:last-child {
  border-right: 0;
}

.change-action-btn {
  display: flex;
  justify-content: space-between;
}

.editor-btn {
  position: fixed;
  right: 0px;
  top: 100px;
  background: #020202;
  font-weight: 600;
  font-size: 18px;
  color: #fff;
  border: none;
  outline: 0;
  padding: 15px 25px;
  cursor: pointer;
}

.color-picker {
  display: flex;
  padding: 15px 20px;
  justify-content: space-between;
  border-bottom: 1px solid #fff;
}

label {
  color: #fff;
  text-transform: capitalize;
  font-weight: 600;
}

.preview-text {
  margin-top: 60px;
  font-size: 20px;
  position: absolute;
  left: 50%;
  transform: translateX(-50%);
}

@media screen and (max-width: 768px) {
  .editor-drawer {
    display: none;
  }

  .editor-btn {
    display: none;
  }
}

@media screen and (max-width: 576px) {
  .pop-up-wrapper {
    width: 350px;
    height: 350px;
  }
  .text-element p {
    font-size: 15px;
  }

  .input-field input {
    height: 30px;
    padding: 19px 15px;
  }

  .submit-btn {
    padding: 8px 10px;
  }

  .smaller-info-text {
    font-size: 11px;
  }

  .star-rating {
    top: -27px;
  }

  .star-rating i {
    font-size: 16px;
  }

  .star-rating .big {
    font-size: 18px;
  }
}

@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}
</style>
