## Backend

Backend code:
```
from flask import Flask, request, jsonify, redirect
import binascii

app = Flask(__name__)

self_host = "http://localhost:5000"
url_mapping = {}

def crc32(data):
    crc = binascii.crc32(data.encode('utf-8'))
    return '{:#0x}'.format(crc)
    
@app.route('/api/v1/shorten', methods=['POST'])
def shorten():
    url = request.json['url']
    crc = crc32(url)
    url_mapping[crc] = url
    return f"{self_host}/s/{crc}"
  
@app.route('/s/<hash>', methods=['GET'])
def get_original(hash):
  return redirect(url_mapping[hash])

   
@app.route("/")
def hello_world():
    return "<p>Hello, World!</p>"
  
  
if __name__ == '__main__':
    app.run(debug=True, port=5000)
```

Run backend code:

```
pip install flask
python3 app.py
```

## Frontend

Shorten.vue
```
<template>
  <div>
    <input v-model="longUrl" placeholder="Your long URL" />
    <button @click="getShortUrl">Shorten</button>
    <input v-model="shortUrl" placeholder="" />
    <button>Copy</button>
  </div>
</template>

<script>
  import axios from 'axios'

  export default {
    name: 'URLShortner',
    data () {
      return {
        longUrl: '',
        shortUrl: ''
      }
    },
    methods: {
      async getShortUrl() {
        try {
          const response = await  axios.post("/api/v1/shorten", {
            "url": this.longUrl
          })
          this.shortUrl = response.data
        } catch (error) {
          console.error(error)
        }
      }
    }
  }

</script>

<style>
</style>
```

Run the code:

```
npm install axios
npm run dev
```
