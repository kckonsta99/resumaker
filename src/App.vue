
<template>
  <main class="container">
    <Sidebar>
      <ToggleSwitch @switch-toggled="toggleEditMode"  label="Edit Mode" off-label="Export Mode"/>
      <ExportPdf v-if="!editing" />
      <div class="sidebar-selection" v-if="editing">
        <div class="sidebar-title">Left column</div>
        <ColorInput label="Highlight color" @color-changed="colors.left.highlight = $event" :default-color="colors.left.highlight"/>
        <ColorInput label="Background color" @color-changed="colors.left.background = $event" :default-color="colors.left.background"/>
        <ColorInput label="text color" @color-changed="colors.left.text = $event" :default-color="colors.left.text"/>
      </div>

      <div class="sidebar-selection" v-if="editing">
        <div class="sidebar-title">Right column</div>
        <ColorInput label="Highlight color" @color-changed="colors.right.highlight = $event" :default-color="colors.right.highlight"/>
        <ColorInput label="Background color" @color-changed="colors.right.background = $event" :default-color="colors.right.background"/>
        <ColorInput label="text color" @color-changed="colors.right.text = $event" :default-color="colors.right.text"/>
      </div>
      <div class="sidebar-selection" v-if="editing">
        <SelectInput label="Headline thickness" :options="[{'name': 'Thin', 'value': '100'}, {'name': 'Medium', 'value': '400'}, {'name': 'Thick', 'value': '600'}]" :default-option="headlineWeight" @update-selection="headlineWeight= $event"/>
        <PercentageInput label="Width of left column" :min="20" :max="80" :current-value="widthLeft" @percentage-changed="widthLeft = $event"/>
      </div>
      
      <div class="sidebar-selection" v-if="editing">
        <ToggleSwitch @switch-toggled="toggleImageDisplay"  label="Show photo"/>
        <SelectInput v-if="showImage" label="Photo shape" :options="[{'name': 'Square', 'value': 'square'}, {'name': 'Round', 'value': 'round'}]" :default-option="imageShape" @update-selection="imageShape= $event"/>

        
        <ImgUpload v-if="showImage" @image-changed="imgUrl = $event" />
      </div>
    </Sidebar>

    <div class="resume-wrapper">
      <div id="resume" class="d-flex" :class="{'edit-off': !editing}" :style="cssVariables">
        <div class="left-col" :style="{width: percentageWidthLeft}">
          
          <ResumeSection>

            <img v-if="showImage" v-bind:src="imgUrl" class="profile-pic" :class="{'circle': imageShape == 'round'}" alt="profile picture">

            <SectionHeadline :headline="headlines[0]" @headline-edited="updateHeadline($event, 0)" :editing="editing" />

            <div :contenteditable="editing" @blur="updateProperty($event, 'introText')">
              {{ introText }}
            </div>
          </ResumeSection>

          <ResumeSection>
            <SectionHeadline :headline="headlines[1]" @headline-edited="updateHeadline($event, 1)" :editing="editing" />   
            <Contact :contact="contact" @edit="updateNestedProperty" :editing="editing" :icon-color="colors.left.highlight"/>
          </ResumeSection>

          <ResumeSection>
            <SectionHeadline :headline="headlines[2]" @headline-edited="updateHeadline($event, 2)" :editing="editing" />
            <ul>
              <li v-for="(skill, index) in skills" :key="index" :contenteditable="editing" @blur="updateNestedProperty($event, 'skills', index)">{{ skill }}</li>
            </ul>
            <EditButtons @add-click="skills.push('new entry')" @remove-click="skills.pop()" :show-remove-btn="skills.length > 0"/>
          </ResumeSection>

          <ResumeSection>
            <SectionHeadline :headline="headlines[3]" @headline-edited="updateHeadline($event, 3)" :editing="editing" />
            <ul >
              <li v-for="(hightlight, HighIndex) in hightlights" :key="HighIndex" :contenteditable="editing" @blur="updateNestedProperty($event, 'hightlights', HighIndex)">{{ hightlight }}</li>
            </ul>
            <EditButtons @add-click="hightlights.push('new entry')" @remove-click="hightlights.pop()" :show-remove-btn="hightlights.length > 0"/>    
          </ResumeSection>  

        </div>
        
        <div class="right-col">
          <div class="rersonal-name" :contenteditable="editing" @blur="updateProperty($event, 'name')">
            {{ name }}
          </div>
          <div class="personal-title" :contenteditable="editing" @blur="updateProperty($event, 'title')">
            {{ title }}
          </div>

          <ResumeSection>
            <div class="d-flex">
              <SectionHeadline :headline="headlines[4]" @headline-edited="updateHeadline($event, 4)" :editing="editing" />
              <EditButtons text-add="Add experience" :show-remove-btn="false" @add-click="addExperience"/>
            </div>



            <div v-for="(item, index) in experience" :key="index" class="inner-section">
              <div class="d-flex justify-content-between">
                <div :contenteditable="editing" class="item-title" @blur="updateExperience($event, 'title', index)">{{ item.title }}</div>



                <EditButtons @remove-click="removeExperience(index)" :show-add-btn="false"/>
              </div>
              <div class="d-flex justify-content-between">
                <div class="details-format">
                  <span :contenteditable="editing" @blur="updateExperience($event, 'company', index)">{{ item.company }}</span>, 
                  <span :contenteditable="editing" @blur="updateExperience($event, 'location', index)">{{ item.location }}</span>
                </div>
                <div class="dates" :contenteditable="editing" @blur="updateExperience($event, 'date', index)">{{ item.date }}</div>
              </div>
              <ul>
                <li class="desc-format" v-for="(desc, DescIndex) in item.description" :key="DescIndex" :contenteditable="editing" @blur="updateExperienceDescription($event, index, DescIndex)"> {{ desc }}</li>
              </ul>
              <EditButtons @add-click="item.description.push('new entry')" @remove-click="item.description.pop()" :show-remove-btn="item.description.length > 0"/>
            </div>
            
          </ResumeSection>
            

          <ResumeSection>      
            <div class="d-flex">
              <SectionHeadline :headline="headlines[5]" @headline-edited="updateHeadline($event, 5)" :editing="editing" />   
              <EditButtons text-add="Add education" :show-remove-btn="false" @add-click="addEducation"/>
            </div>

            <div v-for="(item, index) in education" :key="index" class="inner-section">
              <div class="d-flex justify-content-between">
                <div :contenteditable="editing" dir="ltr" class="item-title" @blur="updateEducation($event, 'title', index)">{{ item.title }}</div>
                <EditButtons @remove-click="removeEducation(index)" :show-add-btn="false"/>
              </div>
              <div class="d-flex justify-content-between">
                <div class="details-format">
                  <span :contenteditable="editing" @blur="updateEducation($event, 'university', index)">{{ item.university }}</span>,
                  <span :contenteditable="editing" @blur="updateEducation($event, 'location', index)">{{ item.location }}</span>
                </div>
                <div class="dates" :contenteditable="editing" @blur="updateEducation($event, 'date', index)">{{ item.date }}</div>
              </div>
              <ul>
                <li class="desc-format" v-for="(desc, DescIndex) in item.description" :key="DescIndex" :contenteditable="editing" @blur="updateEducationDescription($event, index, DescIndex)">{{ desc }}</li>
              </ul>
              <EditButtons @add-click="item.description.push('new entry')" @remove-click="item.description.pop()" :show-remove-btn="item.description.length > 0"/>
            </div>
          </ResumeSection>

        </div>
      </div>
    </div>
  </main>
</template>

<script>

  import ResumeSection from './components/ResumeSection.vue'
  import SectionHeadline from './components/SectionHeadline.vue'
  import Contact from './components/Contact.vue'
  import EditButtons from './components/EditButtons.vue'
  import ToggleSwitch from './components/ToggleSwitch.vue'
  import Sidebar from './components/Sidebar.vue'
  import ColorInput from './components/ColorInput.vue'
  import PercentageInput from './components/PercentageInput.vue'
  import SelectInput from './components/SelectInput.vue'
  import ImgUpload from './components/ImgUpload.vue'
  import ExportPdf from './components/ExportPdf.vue'


  export default {
    components: {
      ResumeSection,
      SectionHeadline,
      Contact,
      EditButtons,
      ToggleSwitch,
      Sidebar,
      ColorInput,
      PercentageInput,
      SelectInput,
      ImgUpload,
      ExportPdf
    },
    data() {
      return {
        colors: {
          left: {
            highlight: '#00BCD4',
            text: '#263238',
            background: '#E0F7FA'
          },
          right: {
            highlight: '#FF5722',
            text: '#424242',
            background: '#FFFFFF'
          }
        },
        name: "Alan Turing",
        title: "Mathematician & Pioneer of Computer Science",
        introText: "I laid the foundations of modern computing, cracked the Enigma code, and envisioned artificial intelligence before it was cool.",
        imgUrl: "/Alan-Turing.webp",
        headlines: ["About me", "Contact", "Skills", "Certifications", "Experience", "Education"],
        contact: {
          phone: "+30 6987654321",
          email: "alan.turing@email.com",
          address: "Wilmslow, Cheshire, England"
        },
        skills: ["Mathematics", "Cryptanalysis", "Theoretical Computer Science", "Logic", "Philosophy", "Artificial Intelligence (Conceptual)", "Machine Design"],
        hightlights: ["Developed the concept of the Turing Machine (1936)", "Key role in breaking the German Enigma code during WWII", "Published 'Computing Machinery and Intelligence' (1950)", "Widely regarded as the father of Artificial Intelligence"],
        experience: [
          {
            title: "Cryptanalyst",
            company: "Government Code and Cypher School",
            location:"Bletchley Park, UK",
            date: "1939 - 1945",
            description: [
              "Led efforts to crack the Enigma encryption used by Nazi Germany.",
              "Designed the electromechanical 'Bombe' machine to decipher codes.",
              "Credited with shortening WWII by several years and saving millions of lives."
            ]
          },
          {
            title: "Theoretical Computer Scientist",
            company: "University of Manchester",
            location:"Manchester, UK",
            date: "1948 - 1954",
            description: [
              "Worked on the design of the Automatic Computing Engine (ACE).",
              "Published foundational research in computing and machine intelligence.",
              "Conducted one of the first experiments in artificial neural networks."
            ]
          }
        ],
        education: [
          {
            title: "PhD in Mathematics",
            university: "Princeton University",
            location: "Princeton, New Jersey, USA",
            date: "1936 - 1938",
            description: [
              "Dissertation on ordinal logic under Alonzo Church.",
              "Introduced the concept of the Turing Machine."
            ]
            
          },
          {
            title: "Mathematics Undergraduate",
            university: "King’s College, University of Cambridge",
            location: "Cambridge, UK",
            date: "1931 - 1934",
            description: [
              "Graduated with first-class honors.",
              "Awarded fellowship for his work on the Central Limit Theorem."
            ]
            
          }
        ],
        editing: true,
        widthLeft: 30,
        imageShape: "round",
        headlineWeight: "400",
        showImage: true
      };
    },
    computed: {
      cssVariables() {
        return{
          '--highlight-color-left': this.colors.left.highlight,
          '--background-color-left': this.colors.left.background,
          '--text-color-left': this.colors.left.text,
          '--highlight-color-right': this.colors.right.highlight,
          '--background-color-right': this.colors.right.background,
          '--text-color-right': this.colors.right.text,
          '--headline-weight': this.headlineWeight
        }
      },
      percentageWidthLeft() {
        return this.widthLeft + "%"
      }
    },
    methods: {
      updateHeadline(newValue, index) {
        this.headlines[index] = newValue;
      },
      updateProperty(event, key) {
        this[key] = event.target.innerText;
      },
      updateNestedProperty(event, key1, key2) {
        this[key1][key2] = event.target.innerText;
      },
      updateExperience(event, key, index) {
        this.experience[index][key] = event.target.innerText;
      },
      updateExperienceDescription(event, index1, index2) {
        this.experience[index1]['description'][index2] = event.target.innerText;
      },
      updateEducation(event, key, index) {
        this.education[index][key] = event.target.innerText;
      },
      updateEducationDescription(event, index1, index2) {
        this.education[index1]['description'][index2] = event.target.innerText;
      },
      // unshift: array method to insert the new object at the beginning
      addExperience() {
        this.experience.unshift({
          title: "Job Title",
          company: "Company",
          location: "Location",
          date: "date range",
          description: [
            "description"
          ]
        });
      },
      addEducation() {
        this.education.unshift({
          title: "Education Title",
          company: "University",
          location: "Location",
          date: "date range",
          description: [
            "description"
          ]
        });
      },
      removeExperience(index) {
        this.experience.splice(index, 1);
      },
      removeEducation(index) {
        this.education.splice(index, 1);
      },
      toggleEditMode(isChecked) {
        this.editing = isChecked;
      },
      toggleImageDisplay(isChecked) {
        this.showImage = isChecked;
      }
    },
  };
</script>

<style scoped>

.resume-wrapper{
  margin-left: auto;
  width: 210mm;
}

#resume {
  box-shadow: rgba(50,50,93,0.25) 0px 13px 27px -5px, rgba(0,0,0,0.3) 0px 8px 16px -8px;

}

#resume.edit-off {
  height: 296mm;
}

@media (min-width: 1350px) {
  .resume-wrapper {
    margin-left: 300px;
  }
}
  @media (min-width: 1600px) {
  .resume-wrapper {
    margin-left: auto;
    margin-right: auto;
  }
}



.left-col {
  /* width: 30%; */
  background-color: var(--background-color-left);
  color: var(--text-color-left);
  border-right: 1px solid var(--highlight-color-left);
  padding: 30px;
}

.right-col {
  width: 70%;
  background-color: var(--background-color-right);
  color: var(--text-color-right);
  padding: 30px;
}

.item-title {
  font-weight: bold;
}

.desc-format {
  margin-left: 8px;
  max-width: 80%;
}

.details-format {
  max-width: 80%;
  opacity: 0.7;
}


.rersonal-name {
  font-weight: 300;
  color: var(--highlight-color-right);
  font-size: 28px;
  border-bottom: 1px solid var(--highlight-color-right);
  margin: 0;
  margin-left: -30px;
  padding-left: 30px;
  padding-bottom: 15px;
}

.personal-title {
  border-bottom: 1px solid var(--highlight-color-right);
  margin: 0 0 20px -30px;
  padding: 15px 0 15px 30px;
  font-weight: 400;
  font-size: 20px;
}

#resume ul {
  padding-inline-start: 16px;
  margin-block-end: 5px;
  margin-block-start: 5px;
}

.profile-pic {
  display: block;
  width: 160px;
  height: 160px;
  border: 3px solid var(--highlight-color-left);
  margin-bottom: 20px;
  object-fit: cover;
  margin-left: auto;
  margin-right: auto;
}
.circle {
  border-radius: 50%;
}

.inner-section {
  margin-bottom: 20px;
}

</style>