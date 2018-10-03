/*
 * Copyright 2017-2017 Amazon.com, Inc. or its affiliates. All Rights Reserved.
 *
 * Licensed under the Apache License, Version 2.0 (the "License"). You may not use this file except in compliance with
 * the License. A copy of the License is located at
 *
 *     http://aws.amazon.com/apache2.0/
 *
 * or in the "license" file accompanying this file. This file is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR
 * CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions
 * and limitations under the License.
 */

<template>
  <div class="container shifted">
    <h1 class="h1">
      AWS Amplify Vue Sample
    </h1>

    <div class="section">
      <h2 class="h2">Sample Structure</h2>
      <pre class="pre chat-list" style="float:left; width:1000px; overflow-y: auto; height: 300px;">
        <ul style="list-style-type: none;">
          <li v-for="item in items">
            <b>{{item.name}}</b>: {{ item.message }}
          </li>
        </ul>

      </pre>
    </div>

    <div class="section">
      <textarea v-model="input" class="form-control" rows="3" placeholder="Nhập tin nhắn" style="width: 70%;" v-on:keyup="onsubmit"></textarea>
    </div>
  </div>
</template>

<script>
import { API, Auth } from 'aws-amplify'
import Pusher from 'pusher-js' // import Pusher
var name = null;
export default {
  name: 'Home',
  data () {
    return {
      items: [
        { message: 'Foo' , name: 'Admin'}
      ],
      input: ''
    }
  },
  created () {
    // ...
    this.subscribe()
  },
  methods: {
    onsubmit: async function(event) {
      if(event.key == "Enter")
      {
        event.preventDefault()
        if (!name) {
          name = await Auth.currentUserInfo().then(res => res.username)
        }
        API.post('test', '/api', {
        body: {
          message: this.input.replace(/\n/g, ""),
          name
        },
        headers: {
          'Content-Type': 'application/json'
        }
      }).then(res => console.log(res))
        this.input = ''
     }
    },
    subscribe () {
      let pusher = new Pusher('af197fe6421e508e6d04', { cluster: 'mt1' })
      pusher.subscribe('chat')
      pusher.bind('message', data => {
        console.log(data)
        this.items.push(data)
      })
    }
  }
}
</script>
