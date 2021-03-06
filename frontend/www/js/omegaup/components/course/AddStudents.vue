<template>
  <div class="omegaup-course-addstudent card">
    <div class="card-body">
      <form
        class="form"
        v-on:submit.prevent="
          $emit('emit-add-student', { participant, participants })
        "
      >
        <div class="form-group">
          <label>{{ T.wordsStudent }}</label>
          <span
            aria-hidden="true"
            class="glyphicon glyphicon-info-sign"
            data-placement="top"
            data-toggle="tooltip"
            v-bind:title="T.courseEditAddStudentsTooltip"
          ></span>
          <omegaup-autocomplete
            v-bind:init="(el) => typeahead.userTypeahead(el)"
            v-model="participant"
          ></omegaup-autocomplete>
        </div>
        <div class="form-group pull-right">
          <button class="btn btn-primary" type="submit">
            {{ T.wordsAddStudent }}
          </button>
        </div>
        <div class="form-group">
          <label>{{ T.wordsMultipleUser }}</label>
          <textarea
            class="form-control pariticipants"
            rows="4"
            v-model="participants"
          ></textarea>
        </div>
        <div class="form-group pull-right">
          <button class="btn btn-primary user-add-bulk" type="submit">
            {{ T.wordsAddStudents }}
          </button>
        </div>
      </form>
      <div v-if="students.length == 0">
        <div class="empty-category">
          {{ T.courseStudentsEmpty }}
        </div>
      </div>
      <table class="table table-striped table-over" v-else="">
        <thead>
          <tr>
            <th>{{ T.wordsUser }}</th>
            <th class="align-right">
              {{ T.contestEditRegisteredAdminDelete }}
            </th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="student in students">
            <td>
              <a v-bind:href="studentProgressUrl(student)">{{
                student.name || student.username
              }}</a>
            </td>
            <td>
              <button
                class="close"
                type="button"
                v-on:click="$emit('emit-remove-student', student)"
              >
                ×
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
    <omegaup-common-requests
      v-bind:data="identityRequests"
      v-bind:text-add-participant="T.wordsAddStudent"
      v-on:emit-accept-request="
        (_, username) => $emit('emit-accept-request', username)
      "
      v-on:emit-deny-request="
        (_, username) => $emit('emit-deny-request', username)
      "
    ></omegaup-common-requests>
  </div>
</template>

<style>
.omegaup-course-addstudent th.align-right {
  text-align: right;
}
</style>

<script lang="ts">
import { Vue, Component, Prop, Watch } from 'vue-property-decorator';
import { omegaup } from '../../omegaup';
import { types } from '../../api_types';
import T from '../../lang';
import * as typeahead from '../../typeahead';
import Autocomplete from '../Autocomplete.vue';
import common_Requests from '../common/Requests.vue';

@Component({
  components: {
    'omegaup-autocomplete': Autocomplete,
    'omegaup-common-requests': common_Requests,
  },
})
export default class CourseAddStudents extends Vue {
  @Prop() courseAlias!: string;
  @Prop() students!: omegaup.CourseStudent[];
  @Prop({ required: false }) identityRequests!: types.IdentityRequest[];

  T = T;
  typeahead = typeahead;
  studentUsername = '';
  participant = '';
  participants = '';
  requests: types.IdentityRequest[] = [];

  studentProgressUrl(student: omegaup.CourseStudent): string {
    return `/course/${this.courseAlias}/student/${student.username}/`;
  }

  @Watch('identityRequests')
  onDataChange(): void {
    this.requests = this.identityRequests;
  }
}
</script>
