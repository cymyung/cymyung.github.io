<a href="https://colab.research.google.com/github/cymyung/data-science/blob/main/Python_data01.ipynb" target="_parent"><img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/></a>


```python
from google.colab import files
```


```python
uploaded = files.upload()
```



     <input type="file" id="files-439ad714-86a7-4761-9a51-5870674d501a" name="files[]" multiple disabled
        style="border:none" />
     <output id="result-439ad714-86a7-4761-9a51-5870674d501a">
      Upload widget is only available when the cell has been executed in the
      current browser session. Please rerun this cell to enable.
      </output>
      <script>// Copyright 2017 Google LLC
//
// Licensed under the Apache License, Version 2.0 (the "License");
// you may not use this file except in compliance with the License.
// You may obtain a copy of the License at
//
//      http://www.apache.org/licenses/LICENSE-2.0
//
// Unless required by applicable law or agreed to in writing, software
// distributed under the License is distributed on an "AS IS" BASIS,
// WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
// See the License for the specific language governing permissions and
// limitations under the License.

/**
 * @fileoverview Helpers for google.colab Python module.
 */
(function(scope) {
function span(text, styleAttributes = {}) {
  const element = document.createElement('span');
  element.textContent = text;
  for (const key of Object.keys(styleAttributes)) {
    element.style[key] = styleAttributes[key];
  }
  return element;
}

// Max number of bytes which will be uploaded at a time.
const MAX_PAYLOAD_SIZE = 100 * 1024;

function _uploadFiles(inputId, outputId) {
  const steps = uploadFilesStep(inputId, outputId);
  const outputElement = document.getElementById(outputId);
  // Cache steps on the outputElement to make it available for the next call
  // to uploadFilesContinue from Python.
  outputElement.steps = steps;

  return _uploadFilesContinue(outputId);
}

// This is roughly an async generator (not supported in the browser yet),
// where there are multiple asynchronous steps and the Python side is going
// to poll for completion of each step.
// This uses a Promise to block the python side on completion of each step,
// then passes the result of the previous step as the input to the next step.
function _uploadFilesContinue(outputId) {
  const outputElement = document.getElementById(outputId);
  const steps = outputElement.steps;

  const next = steps.next(outputElement.lastPromiseValue);
  return Promise.resolve(next.value.promise).then((value) => {
    // Cache the last promise value to make it available to the next
    // step of the generator.
    outputElement.lastPromiseValue = value;
    return next.value.response;
  });
}

/**
 * Generator function which is called between each async step of the upload
 * process.
 * @param {string} inputId Element ID of the input file picker element.
 * @param {string} outputId Element ID of the output display.
 * @return {!Iterable<!Object>} Iterable of next steps.
 */
function* uploadFilesStep(inputId, outputId) {
  const inputElement = document.getElementById(inputId);
  inputElement.disabled = false;

  const outputElement = document.getElementById(outputId);
  outputElement.innerHTML = '';

  const pickedPromise = new Promise((resolve) => {
    inputElement.addEventListener('change', (e) => {
      resolve(e.target.files);
    });
  });

  const cancel = document.createElement('button');
  inputElement.parentElement.appendChild(cancel);
  cancel.textContent = 'Cancel upload';
  const cancelPromise = new Promise((resolve) => {
    cancel.onclick = () => {
      resolve(null);
    };
  });

  // Wait for the user to pick the files.
  const files = yield {
    promise: Promise.race([pickedPromise, cancelPromise]),
    response: {
      action: 'starting',
    }
  };

  cancel.remove();

  // Disable the input element since further picks are not allowed.
  inputElement.disabled = true;

  if (!files) {
    return {
      response: {
        action: 'complete',
      }
    };
  }

  for (const file of files) {
    const li = document.createElement('li');
    li.append(span(file.name, {fontWeight: 'bold'}));
    li.append(span(
        `(${file.type || 'n/a'}) - ${file.size} bytes, ` +
        `last modified: ${
            file.lastModifiedDate ? file.lastModifiedDate.toLocaleDateString() :
                                    'n/a'} - `));
    const percent = span('0% done');
    li.appendChild(percent);

    outputElement.appendChild(li);

    const fileDataPromise = new Promise((resolve) => {
      const reader = new FileReader();
      reader.onload = (e) => {
        resolve(e.target.result);
      };
      reader.readAsArrayBuffer(file);
    });
    // Wait for the data to be ready.
    let fileData = yield {
      promise: fileDataPromise,
      response: {
        action: 'continue',
      }
    };

    // Use a chunked sending to avoid message size limits. See b/62115660.
    let position = 0;
    do {
      const length = Math.min(fileData.byteLength - position, MAX_PAYLOAD_SIZE);
      const chunk = new Uint8Array(fileData, position, length);
      position += length;

      const base64 = btoa(String.fromCharCode.apply(null, chunk));
      yield {
        response: {
          action: 'append',
          file: file.name,
          data: base64,
        },
      };

      let percentDone = fileData.byteLength === 0 ?
          100 :
          Math.round((position / fileData.byteLength) * 100);
      percent.textContent = `${percentDone}% done`;

    } while (position < fileData.byteLength);
  }

  // All done.
  yield {
    response: {
      action: 'complete',
    }
  };
}

scope.google = scope.google || {};
scope.google.colab = scope.google.colab || {};
scope.google.colab._files = {
  _uploadFiles,
  _uploadFilesContinue,
};
})(self);
</script> 


    Saving CCTV_230630.csv to CCTV_230630.csv



```python
import os
os.path.realpath("CCTV_230630.csv")
```


```python
import pandas as pd
file_path = '/content/CCTV_230630.csv'
cctv_seoul = pd.read_csv('CCTV_230630.csv',encoding='euc-kr')
cctv_seoul.head()
```





  <div id="df-a5110d68-4e25-445e-a47c-34ab05e327f2" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Unnamed: 0</th>
      <th>구분</th>
      <th>총계</th>
      <th>2014년 이전</th>
      <th>2014년</th>
      <th>2015년</th>
      <th>2016년</th>
      <th>2017년</th>
      <th>2018년</th>
      <th>2019년</th>
      <th>2020년</th>
      <th>2021년</th>
      <th>2022년</th>
      <th>2023년</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>NaN</td>
      <td>계</td>
      <td>92,991</td>
      <td>5,896</td>
      <td>3,927</td>
      <td>6,416</td>
      <td>8,812</td>
      <td>11,598</td>
      <td>10,887</td>
      <td>13,083</td>
      <td>11,803</td>
      <td>9,396</td>
      <td>9,302</td>
      <td>1,871</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1.0</td>
      <td>종로구</td>
      <td>1,966</td>
      <td>553</td>
      <td>103</td>
      <td>160</td>
      <td>130</td>
      <td>144</td>
      <td>150</td>
      <td>67</td>
      <td>233</td>
      <td>88</td>
      <td>207</td>
      <td>131</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2.0</td>
      <td>중 구</td>
      <td>2,584</td>
      <td>217</td>
      <td>77</td>
      <td>236</td>
      <td>240</td>
      <td>372</td>
      <td>386</td>
      <td>155</td>
      <td>361</td>
      <td>403</td>
      <td>137</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3.0</td>
      <td>용산구</td>
      <td>2,970</td>
      <td>87</td>
      <td>68</td>
      <td>83</td>
      <td>295</td>
      <td>491</td>
      <td>115</td>
      <td>322</td>
      <td>623</td>
      <td>422</td>
      <td>464</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4.0</td>
      <td>성동구</td>
      <td>4,175</td>
      <td>157</td>
      <td>110</td>
      <td>366</td>
      <td>281</td>
      <td>942</td>
      <td>457</td>
      <td>643</td>
      <td>485</td>
      <td>367</td>
      <td>232</td>
      <td>135</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-a5110d68-4e25-445e-a47c-34ab05e327f2')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-a5110d68-4e25-445e-a47c-34ab05e327f2 button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-a5110d68-4e25-445e-a47c-34ab05e327f2');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


<div id="df-eea808a0-542a-49d2-9ea9-6a1d4e97d01c">
  <button class="colab-df-quickchart" onclick="quickchart('df-eea808a0-542a-49d2-9ea9-6a1d4e97d01c')"
            title="Suggest charts"
            style="display:none;">

<svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
     width="24px">
    <g>
        <path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zM9 17H7v-7h2v7zm4 0h-2V7h2v10zm4 0h-2v-4h2v4z"/>
    </g>
</svg>
  </button>

<style>
  .colab-df-quickchart {
      --bg-color: #E8F0FE;
      --fill-color: #1967D2;
      --hover-bg-color: #E2EBFA;
      --hover-fill-color: #174EA6;
      --disabled-fill-color: #AAA;
      --disabled-bg-color: #DDD;
  }

  [theme=dark] .colab-df-quickchart {
      --bg-color: #3B4455;
      --fill-color: #D2E3FC;
      --hover-bg-color: #434B5C;
      --hover-fill-color: #FFFFFF;
      --disabled-bg-color: #3B4455;
      --disabled-fill-color: #666;
  }

  .colab-df-quickchart {
    background-color: var(--bg-color);
    border: none;
    border-radius: 50%;
    cursor: pointer;
    display: none;
    fill: var(--fill-color);
    height: 32px;
    padding: 0;
    width: 32px;
  }

  .colab-df-quickchart:hover {
    background-color: var(--hover-bg-color);
    box-shadow: 0 1px 2px rgba(60, 64, 67, 0.3), 0 1px 3px 1px rgba(60, 64, 67, 0.15);
    fill: var(--button-hover-fill-color);
  }

  .colab-df-quickchart-complete:disabled,
  .colab-df-quickchart-complete:disabled:hover {
    background-color: var(--disabled-bg-color);
    fill: var(--disabled-fill-color);
    box-shadow: none;
  }

  .colab-df-spinner {
    border: 2px solid var(--fill-color);
    border-color: transparent;
    border-bottom-color: var(--fill-color);
    animation:
      spin 1s steps(1) infinite;
  }

  @keyframes spin {
    0% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
      border-left-color: var(--fill-color);
    }
    20% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    30% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
      border-right-color: var(--fill-color);
    }
    40% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    60% {
      border-color: transparent;
      border-right-color: var(--fill-color);
    }
    80% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-bottom-color: var(--fill-color);
    }
    90% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
    }
  }
</style>

  <script>
    async function quickchart(key) {
      const quickchartButtonEl =
        document.querySelector('#' + key + ' button');
      quickchartButtonEl.disabled = true;  // To prevent multiple clicks.
      quickchartButtonEl.classList.add('colab-df-spinner');
      try {
        const charts = await google.colab.kernel.invokeFunction(
            'suggestCharts', [key], {});
      } catch (error) {
        console.error('Error during call to suggestCharts:', error);
      }
      quickchartButtonEl.classList.remove('colab-df-spinner');
      quickchartButtonEl.classList.add('colab-df-quickchart-complete');
    }
    (() => {
      let quickchartButtonEl =
        document.querySelector('#df-eea808a0-542a-49d2-9ea9-6a1d4e97d01c button');
      quickchartButtonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';
    })();
  </script>
</div>

    </div>
  </div>





```python
cctv_seoul.columns[0]
```




    'Unnamed: 0'




```python
cctv_seoul.head()
```





  <div id="df-bbcc5178-db83-4816-a96b-c36870516798" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>구별</th>
      <th>구별</th>
      <th>총계</th>
      <th>2014년 이전</th>
      <th>2014년</th>
      <th>2015년</th>
      <th>2016년</th>
      <th>2017년</th>
      <th>2018년</th>
      <th>2019년</th>
      <th>2020년</th>
      <th>2021년</th>
      <th>2022년</th>
      <th>2023년</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>NaN</td>
      <td>계</td>
      <td>92,991</td>
      <td>5,896</td>
      <td>3,927</td>
      <td>6,416</td>
      <td>8,812</td>
      <td>11,598</td>
      <td>10,887</td>
      <td>13,083</td>
      <td>11,803</td>
      <td>9,396</td>
      <td>9,302</td>
      <td>1,871</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1.0</td>
      <td>종로구</td>
      <td>1,966</td>
      <td>553</td>
      <td>103</td>
      <td>160</td>
      <td>130</td>
      <td>144</td>
      <td>150</td>
      <td>67</td>
      <td>233</td>
      <td>88</td>
      <td>207</td>
      <td>131</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2.0</td>
      <td>중 구</td>
      <td>2,584</td>
      <td>217</td>
      <td>77</td>
      <td>236</td>
      <td>240</td>
      <td>372</td>
      <td>386</td>
      <td>155</td>
      <td>361</td>
      <td>403</td>
      <td>137</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3.0</td>
      <td>용산구</td>
      <td>2,970</td>
      <td>87</td>
      <td>68</td>
      <td>83</td>
      <td>295</td>
      <td>491</td>
      <td>115</td>
      <td>322</td>
      <td>623</td>
      <td>422</td>
      <td>464</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4.0</td>
      <td>성동구</td>
      <td>4,175</td>
      <td>157</td>
      <td>110</td>
      <td>366</td>
      <td>281</td>
      <td>942</td>
      <td>457</td>
      <td>643</td>
      <td>485</td>
      <td>367</td>
      <td>232</td>
      <td>135</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-bbcc5178-db83-4816-a96b-c36870516798')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-bbcc5178-db83-4816-a96b-c36870516798 button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-bbcc5178-db83-4816-a96b-c36870516798');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


<div id="df-7a62c47b-03ba-4da3-bacd-671b1c11eca9">
  <button class="colab-df-quickchart" onclick="quickchart('df-7a62c47b-03ba-4da3-bacd-671b1c11eca9')"
            title="Suggest charts"
            style="display:none;">

<svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
     width="24px">
    <g>
        <path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zM9 17H7v-7h2v7zm4 0h-2V7h2v10zm4 0h-2v-4h2v4z"/>
    </g>
</svg>
  </button>

<style>
  .colab-df-quickchart {
      --bg-color: #E8F0FE;
      --fill-color: #1967D2;
      --hover-bg-color: #E2EBFA;
      --hover-fill-color: #174EA6;
      --disabled-fill-color: #AAA;
      --disabled-bg-color: #DDD;
  }

  [theme=dark] .colab-df-quickchart {
      --bg-color: #3B4455;
      --fill-color: #D2E3FC;
      --hover-bg-color: #434B5C;
      --hover-fill-color: #FFFFFF;
      --disabled-bg-color: #3B4455;
      --disabled-fill-color: #666;
  }

  .colab-df-quickchart {
    background-color: var(--bg-color);
    border: none;
    border-radius: 50%;
    cursor: pointer;
    display: none;
    fill: var(--fill-color);
    height: 32px;
    padding: 0;
    width: 32px;
  }

  .colab-df-quickchart:hover {
    background-color: var(--hover-bg-color);
    box-shadow: 0 1px 2px rgba(60, 64, 67, 0.3), 0 1px 3px 1px rgba(60, 64, 67, 0.15);
    fill: var(--button-hover-fill-color);
  }

  .colab-df-quickchart-complete:disabled,
  .colab-df-quickchart-complete:disabled:hover {
    background-color: var(--disabled-bg-color);
    fill: var(--disabled-fill-color);
    box-shadow: none;
  }

  .colab-df-spinner {
    border: 2px solid var(--fill-color);
    border-color: transparent;
    border-bottom-color: var(--fill-color);
    animation:
      spin 1s steps(1) infinite;
  }

  @keyframes spin {
    0% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
      border-left-color: var(--fill-color);
    }
    20% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    30% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
      border-right-color: var(--fill-color);
    }
    40% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    60% {
      border-color: transparent;
      border-right-color: var(--fill-color);
    }
    80% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-bottom-color: var(--fill-color);
    }
    90% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
    }
  }
</style>

  <script>
    async function quickchart(key) {
      const quickchartButtonEl =
        document.querySelector('#' + key + ' button');
      quickchartButtonEl.disabled = true;  // To prevent multiple clicks.
      quickchartButtonEl.classList.add('colab-df-spinner');
      try {
        const charts = await google.colab.kernel.invokeFunction(
            'suggestCharts', [key], {});
      } catch (error) {
        console.error('Error during call to suggestCharts:', error);
      }
      quickchartButtonEl.classList.remove('colab-df-spinner');
      quickchartButtonEl.classList.add('colab-df-quickchart-complete');
    }
    (() => {
      let quickchartButtonEl =
        document.querySelector('#df-7a62c47b-03ba-4da3-bacd-671b1c11eca9 button');
      quickchartButtonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';
    })();
  </script>
</div>

    </div>
  </div>





```python
cctv_seoul.rename(columns = {cctv_seoul.columns[1]:'구별'},inplace = True)
```


```python
cctv_seoul.rename(columns = {cctv_seoul.columns[0]:'번호'},inplace = True)
```


```python
cctv_seoul.drop(columns = cctv_seoul.columns[0])
```





  <div id="df-8496bf1d-b6ec-44da-9db1-ed100f7e3def" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>구분</th>
      <th>총계</th>
      <th>2014년 이전</th>
      <th>2014년</th>
      <th>2015년</th>
      <th>2016년</th>
      <th>2017년</th>
      <th>2018년</th>
      <th>2019년</th>
      <th>2020년</th>
      <th>2021년</th>
      <th>2022년</th>
      <th>2023년</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>계</td>
      <td>92,991</td>
      <td>5,896</td>
      <td>3,927</td>
      <td>6,416</td>
      <td>8,812</td>
      <td>11,598</td>
      <td>10,887</td>
      <td>13,083</td>
      <td>11,803</td>
      <td>9,396</td>
      <td>9,302</td>
      <td>1,871</td>
    </tr>
    <tr>
      <th>1</th>
      <td>종로구</td>
      <td>1,966</td>
      <td>553</td>
      <td>103</td>
      <td>160</td>
      <td>130</td>
      <td>144</td>
      <td>150</td>
      <td>67</td>
      <td>233</td>
      <td>88</td>
      <td>207</td>
      <td>131</td>
    </tr>
    <tr>
      <th>2</th>
      <td>중 구</td>
      <td>2,584</td>
      <td>217</td>
      <td>77</td>
      <td>236</td>
      <td>240</td>
      <td>372</td>
      <td>386</td>
      <td>155</td>
      <td>361</td>
      <td>403</td>
      <td>137</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>용산구</td>
      <td>2,970</td>
      <td>87</td>
      <td>68</td>
      <td>83</td>
      <td>295</td>
      <td>491</td>
      <td>115</td>
      <td>322</td>
      <td>623</td>
      <td>422</td>
      <td>464</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>성동구</td>
      <td>4,175</td>
      <td>157</td>
      <td>110</td>
      <td>366</td>
      <td>281</td>
      <td>942</td>
      <td>457</td>
      <td>643</td>
      <td>485</td>
      <td>367</td>
      <td>232</td>
      <td>135</td>
    </tr>
    <tr>
      <th>5</th>
      <td>광진구</td>
      <td>3,592</td>
      <td>130</td>
      <td>176</td>
      <td>95</td>
      <td>40</td>
      <td>666</td>
      <td>460</td>
      <td>712</td>
      <td>190</td>
      <td>738</td>
      <td>273</td>
      <td>112</td>
    </tr>
    <tr>
      <th>6</th>
      <td>동대문구</td>
      <td>2,759</td>
      <td>3</td>
      <td>5</td>
      <td>72</td>
      <td>774</td>
      <td>702</td>
      <td>201</td>
      <td>218</td>
      <td>229</td>
      <td>229</td>
      <td>317</td>
      <td>9</td>
    </tr>
    <tr>
      <th>7</th>
      <td>중랑구</td>
      <td>4,193</td>
      <td>555</td>
      <td>104</td>
      <td>145</td>
      <td>153</td>
      <td>170</td>
      <td>215</td>
      <td>1074</td>
      <td>976</td>
      <td>507</td>
      <td>294</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>8</th>
      <td>성북구</td>
      <td>4,957</td>
      <td>318</td>
      <td>229</td>
      <td>322</td>
      <td>594</td>
      <td>811</td>
      <td>867</td>
      <td>714</td>
      <td>253</td>
      <td>407</td>
      <td>338</td>
      <td>104</td>
    </tr>
    <tr>
      <th>9</th>
      <td>강북구</td>
      <td>3,321</td>
      <td>21</td>
      <td>16</td>
      <td>68</td>
      <td>210</td>
      <td>4</td>
      <td>375</td>
      <td>963</td>
      <td>569</td>
      <td>298</td>
      <td>797</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>10</th>
      <td>도봉구</td>
      <td>2,385</td>
      <td>98</td>
      <td>54</td>
      <td>27</td>
      <td>91</td>
      <td>116</td>
      <td>183</td>
      <td>195</td>
      <td>179</td>
      <td>592</td>
      <td>591</td>
      <td>259</td>
    </tr>
    <tr>
      <th>11</th>
      <td>노원구</td>
      <td>2,626</td>
      <td>238</td>
      <td>75</td>
      <td>509</td>
      <td>329</td>
      <td>171</td>
      <td>216</td>
      <td>324</td>
      <td>387</td>
      <td>215</td>
      <td>144</td>
      <td>18</td>
    </tr>
    <tr>
      <th>12</th>
      <td>은평구</td>
      <td>4,653</td>
      <td>61</td>
      <td>332</td>
      <td>329</td>
      <td>555</td>
      <td>403</td>
      <td>635</td>
      <td>1,057</td>
      <td>288</td>
      <td>471</td>
      <td>522</td>
      <td>0</td>
    </tr>
    <tr>
      <th>13</th>
      <td>서대문구</td>
      <td>3,538</td>
      <td>1,012</td>
      <td>114</td>
      <td>109</td>
      <td>277</td>
      <td>416</td>
      <td>246</td>
      <td>325</td>
      <td>441</td>
      <td>331</td>
      <td>174</td>
      <td>93</td>
    </tr>
    <tr>
      <th>14</th>
      <td>마포구</td>
      <td>2,638</td>
      <td>61</td>
      <td>109</td>
      <td>170</td>
      <td>458</td>
      <td>376</td>
      <td>368</td>
      <td>494</td>
      <td>298</td>
      <td>177</td>
      <td>117</td>
      <td>10</td>
    </tr>
    <tr>
      <th>15</th>
      <td>양천구</td>
      <td>3,890</td>
      <td>369</td>
      <td>129</td>
      <td>243</td>
      <td>423</td>
      <td>564</td>
      <td>833</td>
      <td>311</td>
      <td>352</td>
      <td>374</td>
      <td>292</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>16</th>
      <td>강서구</td>
      <td>3,353</td>
      <td>132</td>
      <td>200</td>
      <td>192</td>
      <td>167</td>
      <td>507</td>
      <td>259</td>
      <td>455</td>
      <td>363</td>
      <td>441</td>
      <td>596</td>
      <td>41</td>
    </tr>
    <tr>
      <th>17</th>
      <td>구로구</td>
      <td>4,831</td>
      <td>307</td>
      <td>160</td>
      <td>263</td>
      <td>351</td>
      <td>559</td>
      <td>542</td>
      <td>789</td>
      <td>652</td>
      <td>689</td>
      <td>341</td>
      <td>178</td>
    </tr>
    <tr>
      <th>18</th>
      <td>금천구</td>
      <td>2,885</td>
      <td>NaN</td>
      <td>43</td>
      <td>350</td>
      <td>132</td>
      <td>196</td>
      <td>539</td>
      <td>366</td>
      <td>513</td>
      <td>207</td>
      <td>304</td>
      <td>235</td>
    </tr>
    <tr>
      <th>19</th>
      <td>영등포구</td>
      <td>4,660</td>
      <td>190</td>
      <td>295</td>
      <td>360</td>
      <td>285</td>
      <td>422</td>
      <td>803</td>
      <td>153</td>
      <td>1357</td>
      <td>329</td>
      <td>450</td>
      <td>16</td>
    </tr>
    <tr>
      <th>20</th>
      <td>동작구</td>
      <td>2,690</td>
      <td>88</td>
      <td>478</td>
      <td>127</td>
      <td>255</td>
      <td>270</td>
      <td>299</td>
      <td>311</td>
      <td>433</td>
      <td>30</td>
      <td>373</td>
      <td>26</td>
    </tr>
    <tr>
      <th>21</th>
      <td>관악구</td>
      <td>5,642</td>
      <td>592</td>
      <td>277</td>
      <td>448</td>
      <td>607</td>
      <td>675</td>
      <td>663</td>
      <td>889</td>
      <td>404</td>
      <td>549</td>
      <td>135</td>
      <td>403</td>
    </tr>
    <tr>
      <th>22</th>
      <td>서초구</td>
      <td>4,995</td>
      <td>160</td>
      <td>70</td>
      <td>559</td>
      <td>518</td>
      <td>1054</td>
      <td>426</td>
      <td>344</td>
      <td>422</td>
      <td>566</td>
      <td>876</td>
      <td>0</td>
    </tr>
    <tr>
      <th>23</th>
      <td>강남구</td>
      <td>7,243</td>
      <td>121</td>
      <td>573</td>
      <td>826</td>
      <td>1295</td>
      <td>989</td>
      <td>744</td>
      <td>790</td>
      <td>923</td>
      <td>514</td>
      <td>468</td>
      <td>0</td>
    </tr>
    <tr>
      <th>24</th>
      <td>송파구</td>
      <td>3,253</td>
      <td>149</td>
      <td>74</td>
      <td>183</td>
      <td>126</td>
      <td>227</td>
      <td>526</td>
      <td>1050</td>
      <td>237</td>
      <td>151</td>
      <td>431</td>
      <td>99</td>
    </tr>
    <tr>
      <th>25</th>
      <td>강동구</td>
      <td>3,192</td>
      <td>277</td>
      <td>56</td>
      <td>174</td>
      <td>226</td>
      <td>351</td>
      <td>379</td>
      <td>362</td>
      <td>635</td>
      <td>301</td>
      <td>429</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-8496bf1d-b6ec-44da-9db1-ed100f7e3def')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-8496bf1d-b6ec-44da-9db1-ed100f7e3def button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-8496bf1d-b6ec-44da-9db1-ed100f7e3def');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


<div id="df-4a648458-4ac0-4d17-8a4a-51c25660d3f3">
  <button class="colab-df-quickchart" onclick="quickchart('df-4a648458-4ac0-4d17-8a4a-51c25660d3f3')"
            title="Suggest charts"
            style="display:none;">

<svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
     width="24px">
    <g>
        <path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zM9 17H7v-7h2v7zm4 0h-2V7h2v10zm4 0h-2v-4h2v4z"/>
    </g>
</svg>
  </button>

<style>
  .colab-df-quickchart {
      --bg-color: #E8F0FE;
      --fill-color: #1967D2;
      --hover-bg-color: #E2EBFA;
      --hover-fill-color: #174EA6;
      --disabled-fill-color: #AAA;
      --disabled-bg-color: #DDD;
  }

  [theme=dark] .colab-df-quickchart {
      --bg-color: #3B4455;
      --fill-color: #D2E3FC;
      --hover-bg-color: #434B5C;
      --hover-fill-color: #FFFFFF;
      --disabled-bg-color: #3B4455;
      --disabled-fill-color: #666;
  }

  .colab-df-quickchart {
    background-color: var(--bg-color);
    border: none;
    border-radius: 50%;
    cursor: pointer;
    display: none;
    fill: var(--fill-color);
    height: 32px;
    padding: 0;
    width: 32px;
  }

  .colab-df-quickchart:hover {
    background-color: var(--hover-bg-color);
    box-shadow: 0 1px 2px rgba(60, 64, 67, 0.3), 0 1px 3px 1px rgba(60, 64, 67, 0.15);
    fill: var(--button-hover-fill-color);
  }

  .colab-df-quickchart-complete:disabled,
  .colab-df-quickchart-complete:disabled:hover {
    background-color: var(--disabled-bg-color);
    fill: var(--disabled-fill-color);
    box-shadow: none;
  }

  .colab-df-spinner {
    border: 2px solid var(--fill-color);
    border-color: transparent;
    border-bottom-color: var(--fill-color);
    animation:
      spin 1s steps(1) infinite;
  }

  @keyframes spin {
    0% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
      border-left-color: var(--fill-color);
    }
    20% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    30% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
      border-right-color: var(--fill-color);
    }
    40% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    60% {
      border-color: transparent;
      border-right-color: var(--fill-color);
    }
    80% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-bottom-color: var(--fill-color);
    }
    90% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
    }
  }
</style>

  <script>
    async function quickchart(key) {
      const quickchartButtonEl =
        document.querySelector('#' + key + ' button');
      quickchartButtonEl.disabled = true;  // To prevent multiple clicks.
      quickchartButtonEl.classList.add('colab-df-spinner');
      try {
        const charts = await google.colab.kernel.invokeFunction(
            'suggestCharts', [key], {});
      } catch (error) {
        console.error('Error during call to suggestCharts:', error);
      }
      quickchartButtonEl.classList.remove('colab-df-spinner');
      quickchartButtonEl.classList.add('colab-df-quickchart-complete');
    }
    (() => {
      let quickchartButtonEl =
        document.querySelector('#df-4a648458-4ac0-4d17-8a4a-51c25660d3f3 button');
      quickchartButtonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';
    })();
  </script>
</div>

    </div>
  </div>





```python
files.upload()
```



     <input type="file" id="files-b87319a3-fb86-41cf-90f0-ab8cb5c2f18a" name="files[]" multiple disabled
        style="border:none" />
     <output id="result-b87319a3-fb86-41cf-90f0-ab8cb5c2f18a">
      Upload widget is only available when the cell has been executed in the
      current browser session. Please rerun this cell to enable.
      </output>
      <script>// Copyright 2017 Google LLC
//
// Licensed under the Apache License, Version 2.0 (the "License");
// you may not use this file except in compliance with the License.
// You may obtain a copy of the License at
//
//      http://www.apache.org/licenses/LICENSE-2.0
//
// Unless required by applicable law or agreed to in writing, software
// distributed under the License is distributed on an "AS IS" BASIS,
// WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
// See the License for the specific language governing permissions and
// limitations under the License.

/**
 * @fileoverview Helpers for google.colab Python module.
 */
(function(scope) {
function span(text, styleAttributes = {}) {
  const element = document.createElement('span');
  element.textContent = text;
  for (const key of Object.keys(styleAttributes)) {
    element.style[key] = styleAttributes[key];
  }
  return element;
}

// Max number of bytes which will be uploaded at a time.
const MAX_PAYLOAD_SIZE = 100 * 1024;

function _uploadFiles(inputId, outputId) {
  const steps = uploadFilesStep(inputId, outputId);
  const outputElement = document.getElementById(outputId);
  // Cache steps on the outputElement to make it available for the next call
  // to uploadFilesContinue from Python.
  outputElement.steps = steps;

  return _uploadFilesContinue(outputId);
}

// This is roughly an async generator (not supported in the browser yet),
// where there are multiple asynchronous steps and the Python side is going
// to poll for completion of each step.
// This uses a Promise to block the python side on completion of each step,
// then passes the result of the previous step as the input to the next step.
function _uploadFilesContinue(outputId) {
  const outputElement = document.getElementById(outputId);
  const steps = outputElement.steps;

  const next = steps.next(outputElement.lastPromiseValue);
  return Promise.resolve(next.value.promise).then((value) => {
    // Cache the last promise value to make it available to the next
    // step of the generator.
    outputElement.lastPromiseValue = value;
    return next.value.response;
  });
}

/**
 * Generator function which is called between each async step of the upload
 * process.
 * @param {string} inputId Element ID of the input file picker element.
 * @param {string} outputId Element ID of the output display.
 * @return {!Iterable<!Object>} Iterable of next steps.
 */
function* uploadFilesStep(inputId, outputId) {
  const inputElement = document.getElementById(inputId);
  inputElement.disabled = false;

  const outputElement = document.getElementById(outputId);
  outputElement.innerHTML = '';

  const pickedPromise = new Promise((resolve) => {
    inputElement.addEventListener('change', (e) => {
      resolve(e.target.files);
    });
  });

  const cancel = document.createElement('button');
  inputElement.parentElement.appendChild(cancel);
  cancel.textContent = 'Cancel upload';
  const cancelPromise = new Promise((resolve) => {
    cancel.onclick = () => {
      resolve(null);
    };
  });

  // Wait for the user to pick the files.
  const files = yield {
    promise: Promise.race([pickedPromise, cancelPromise]),
    response: {
      action: 'starting',
    }
  };

  cancel.remove();

  // Disable the input element since further picks are not allowed.
  inputElement.disabled = true;

  if (!files) {
    return {
      response: {
        action: 'complete',
      }
    };
  }

  for (const file of files) {
    const li = document.createElement('li');
    li.append(span(file.name, {fontWeight: 'bold'}));
    li.append(span(
        `(${file.type || 'n/a'}) - ${file.size} bytes, ` +
        `last modified: ${
            file.lastModifiedDate ? file.lastModifiedDate.toLocaleDateString() :
                                    'n/a'} - `));
    const percent = span('0% done');
    li.appendChild(percent);

    outputElement.appendChild(li);

    const fileDataPromise = new Promise((resolve) => {
      const reader = new FileReader();
      reader.onload = (e) => {
        resolve(e.target.result);
      };
      reader.readAsArrayBuffer(file);
    });
    // Wait for the data to be ready.
    let fileData = yield {
      promise: fileDataPromise,
      response: {
        action: 'continue',
      }
    };

    // Use a chunked sending to avoid message size limits. See b/62115660.
    let position = 0;
    do {
      const length = Math.min(fileData.byteLength - position, MAX_PAYLOAD_SIZE);
      const chunk = new Uint8Array(fileData, position, length);
      position += length;

      const base64 = btoa(String.fromCharCode.apply(null, chunk));
      yield {
        response: {
          action: 'append',
          file: file.name,
          data: base64,
        },
      };

      let percentDone = fileData.byteLength === 0 ?
          100 :
          Math.round((position / fileData.byteLength) * 100);
      percent.textContent = `${percentDone}% done`;

    } while (position < fileData.byteLength);
  }

  // All done.
  yield {
    response: {
      action: 'complete',
    }
  };
}

scope.google = scope.google || {};
scope.google.colab = scope.google.colab || {};
scope.google.colab._files = {
  _uploadFiles,
  _uploadFilesContinue,
};
})(self);
</script> 


    Saving population_240101.xlsx to population_240101.xlsx





    {'population_240101.xlsx': b'PK\x03\x04\x14\x00\x08\x08\x08\x00\xbbY*X\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x11\x00\x00\x00docProps/core.xml\xad\x91\xcdJ\xc40\x14F\xf7}\x8a\x90}\x9b\xa4uDB\xdbA\x94\x01Aq\xc0\xca\x88\xbb\x90^\xdbb\xf3C\x12\xed\xf8\xf6f:cEq\xe9\xf2\xe6;\xdf\xe1rS\xae\xf7jD\xef\xe0\xfc`t\x85YF1\x02-M;\xe8\xae\xc2\x8f\xcd&\xbd\xc0\xeb:IJi\x1cl\x9d\xb1\xe0\xc2\x00\x1e\xc5\x96\xf6\x15\xeeC\xb0\x9c\x10/{P\xc2g1\xd61y1N\x89\x10G\xd7\x11+\xe4\xab\xe8\x80\xe4\x94\x9e\x13\x05A\xb4"\x08r\xb0\xa5v\xd1\xe1\xa3\x8fK\xfb\xef\xcaV.J\xfb\xe6\xc6Y\xd0J\x02#(\xd0\xc1\x13\x961\xf2\xcd\x06p\xca\xffY\x98\x93\x85\xdc\xfba\xa1\xa6i\xca\xa6b\xe6\xe2F\x8c<\xdd\xdd>\xcc\xcb\xa7\x83\xf6Ah\t\xb8N\x10*Ov.\x1d\x88\x00-\x8a\x0e\x1e>,T\xf8+\xd9\x15W\xd7\xcd\x06\xd79\xcd\xcfR\xcaRF\x1b\x9asV\xf0\xd5\xea\xb9$\xbf\xfa\'\xe7q4\xae\xbe\x8cg\xe9\x01m\xefo\x0e\xe8\xf2\x9c\x94\xe4\xe7\xd7\xd5\xc9\'PK\x07\x08\xfdV\x0fr\x04\x01\x00\x00\xf4\x01\x00\x00PK\x03\x04\x14\x00\x08\x08\x08\x00\xbbY*X\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x0b\x00\x00\x00_rels/.rels\xad\x92\xc1N\xc30\x0c\x86\xef}\x8a(\xf7\xd5\xdd\x90\x10BMwAH\xbbMh<@H\xdc6j\x13G\x89\x07\xe5\xed\t\x07\x04C\x0cv\xe0\x18\xe7\xf7\xe7O\xb2\xdb\xed\xe2g\xf1\x8c);\nJ\xae\xebF\n\x0c\x86\xac\x0b\x83\x92\x8f\x87\xfb\xd5\x8d\xdcvU\xd5>\xe0\xac\xb9d\xf2\xe8b\x16\xa5)d%G\xe6x\x0b\x90\xcd\x88^\xe7\x9a"\x86\xf2\xd3S\xf2\x9a\xcb3\r\x10\xb5\x99\xf4\x80\xb0i\x9akH_\x19\xb2\xab\x848\xc1\x8a\x9dU2\xed\xecZ\x8a\xc3k\xc4K\xf0\xd4\xf7\xce\xe0\x1d\x99\xa3\xc7\xc0?L\xf9\x96(d\x9d\x06d%\x97\x19^(MODS]\xa0\x12\xce\xeal\xfeS\x07\x17\xc6`\xd1\xaeb*\xfd\x89\x1d\xe6O\'Kf_\xca\x19t\x8c\x7fH]].u~\x05\xe0\x91\xb5\xd5\xac\xc1P\xc2\xdf\x95\xde\x13\x1fN-\x9c\\CW\xbd\x01PK\x07\x08W(^#\xe3\x00\x00\x00F\x02\x00\x00PK\x03\x04\x14\x00\x08\x08\x08\x00\xbbY*X\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x13\x00\x00\x00[Content_Types].xml\xb5\x94\xcbn\xc20\x10E\xf7\xf9\n\xcb\xdb*6\xb0\xa8\xaa*\x81E\x1f\xcb\x16\xa9\xf4\x03\\{B,\xfc\x92m(\xfc}\'\xa1o\x15\n*]Y\xd6\xdc{\xcf\xcd(r5Y[CV\x10\x93\xf6\xae\xa6C6\xa0\x04\x9c\xf4J\xbbyM\x1fg\xb7\xe5\x05\x9d\x8c\x8b\xa2\x9am\x02$\x82b\x97j\xda\xe6\x1c.9O\xb2\x05+\x12\xf3\x01\x1cN\x1a\x1f\xad\xc8x\x8ds\x1e\x84\\\x889\xf0\xd1`p\xce\xa5w\x19\\.s\x97A\xc7\x05!\xd554bi2\xb9Y\xe3d\xcb\x8e`\x12%W[m\x87\xab\xa9\x08\xc1h)2\xce\xf9\xca\xa9o\xa0\xf2\x15\xc2\xd0\xd9kR\xabC:C\x01\xe5\xbb \xddp7\xe3\xc3z\x8f+\x89Z\x01\x99\x8a\x98\xef\x84E!W^N\xa3\x0f\x89\xa3\x85\xed\x0f\xfa\xa1\xaco\x1a-\x013\x96\x16-\x0c\xbaN\nT\x190\x12b\xd6\xf0\xb9\xf9^\xbc\xf4\x11\x8e\xe7\xbf-\xabs\x1f\x0e]\x1b\x9eZ\x11A=\xe4\x88\xbfD\xfa\xf3w\xa7\x10A\xa8\xd4\x02dk\xd8\x97\xec\x03\xaa\xe4\x8d\x81\x93w\xe8C\x7f\x87?\xfb\xb8x\xf2~q\xf2\x15\xe0\xc9\xac\xd0\xee\xb0\n\xbd>\xf1\xfe\x18\x9e\xb8\xcb{\xfe\xf1UF\xff]\xa5\xe2\xfd\x134.^\x00PK\x07\x08\xfep\x9e\x84=\x01\x00\x00\xb3\x04\x00\x00PK\x03\x04\x14\x00\x08\x08\x08\x00\xbbY*X\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x10\x00\x00\x00docProps/app.xmlM\x8e\xc1\n\xc20\x10D\xef~E\xc8\xbd\xdd\xeaAD\xd2\x94\x82\x08\x9e\xecA? \xa4\xdb6\xd0lB\xb2J?\xdf\x9c\xd4\xe3\xcc0\x8f\xa7\xba\xcd\xaf\xe2\x8d)\xbb@\xad\xdc\xd7\x8d\x14H6\x8c\x8e\xe6V>\x1f\xd7\xea$;\xbdSC\n\x11\x13;\xcc\xa2\x1c(\xb7ra\x8eg\x80l\x17\xf4&\xd7e\xa6\xb2L!y\xc3%\xa6\x19\xc249\x8b\x97`_\x1e\x89\xe1\xd04G\xc0\x8d\x91F\x1c\xab\xf8\x05J\xad\xfa\x18Wg\r\x17\x07\xddGS\x90b\xb8\xdf\x14\xfc\xf7\n~\x0e\xfa\x03PK\x07\x08\xe1|w\xd8\x91\x00\x00\x00\xb7\x00\x00\x00PK\x03\x04\x14\x00\x08\x08\x08\x00\xbbY*X\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x14\x00\x00\x00xl/sharedStrings.xml}U\xddn\x12Q\x10\xbe\xf7)N\xf6\xc2TS\xf7\x8f\xb6i\x10\xe8\x85\x8d\xb1\x89\x89\x89i\xaf\x8ci\x10\xd6B\x84]d\x97\xc6+\xd3\xd6\xa5\xc1B\x04\xa2\xd8\x16\xa1\x01KBk0\xd2\x02\xcd6\xe1i\xbc\xdcs\xf6\x1d\x9c\x05k+;\xf4j\xf7\xcc7\xf3\xcd\xdf93\x81\xa5w\xc9\x04\xd9T\xd2z\\S\x83\x9c\xc4\x8b\x1cQ\xd4\x88\x16\x8d\xab\x1bAnm\xf5\xf1\x83En)t\'\xa0\xeb\x06\x89h\x19\xd5\x00\x9dy\x89#\x195\xfe6\xa3<\x1aK\xe6\x179\x024\xaa\x1e\xe4b\x86\x91\xf2\x0b\x82\x1e\x89)\xc9\xb0\xcek)E\x05\xe4\xb5\x96N\x86\r8\xa67\x04=\x95V\xc2Q=\xa6(F2!\xc8\xa2\xb8 $\xc3q\x95\x0b\x05\xf4x(`\x84h\xf1\x90\xf6\xcc\x19\xe9^@0B\x01\xc1\x15\xfe\x07\xc8\x1e@\x16e\x1f\x91\x859T\xee\xf3\xca\x99i\xd1\xc2\x16\x99\x19\x7f=tv\xcf$3\xf4G\xd6\x038\x95\x9a=\xf8\xc9\xea\x16\x0e\xd3\xcfg\xb4Yg\x87\xd6mJc\x9f4\x7f\t\x1a\xf6\xa0\x83+-\xcc\x83\x1a\xab\xf7\xd9\x87m\xbb\xd7\xa0\x8dKvT\x9aB\xb7[\x80p=\x91\xec\x9c\x80\x85Gw\xbf\x83H\x9d\xca\xa9\x97A\xf8\xdf\xf0{\x966!\xf5\x8e\x87\xb1U\xc6\xa4\xd5S\xb6\xd3\xc5\x00\xf3\x0cz\x88\x00v\xbf\xcc\xda&\x02\xb8-\x87ju,\xdc9=B\xfd\x83\x9b\x8bm\xccM\xb7\x82\x03\xb4h\xd2\xfeG\x0c\xc8Z\xec[\t\xf3Q\xdfr\x8a\x98\x053kS#\xa6\xed\x9cS\xec`F_\xcb\xec\x1c+0D\x0c\x84\x180\xe8\xe0-\xb1\xad\x1cN\xc5\x0e\xb6\xe1\x82\xe2\xfe\xa1\xcc\x0c-\xa5\xdd\xdfb\x95,\x9e&\xeb\xe7\xa6\xd4x\xe7\x04\xb3\xd8m:\x85\xc2\x14\x0b\xf4V\xfc\xde\xcf\x13gw\x00\xb7\xd3)\xd7V\x96\'\xe1\xe5\xd5uY\x94Dqn\xfd\x99\x08SD\xf4\xddj\x0f\x8f\xc7\x13\xd1\xf1\x90\xfe\x1a\xfe}\xb5u\xaca.\x03kv\x9dj\xc1\xb6\xbavw\xf2\x95\xb83\xcf\xaf\xa7\xc2\x11%\xc8\xc1P\xd3\x95\xf4\xa6\xc2\x85^\xd0\x0b\x13\xd4_\x12w\xfc\x88\xf2\xfb\xd1\xc7G\x08J~Qc\xe7\x07Xq\xab\x96\xb3w\t\xe3\x8e\xe5k\xb3dB\x00\xec\xb4g\x8dSCY\x8fJ\xf4\xb8@\xf3-V\xed\xb3\xfa\x10y\xf4\x10\xd3\x1c/J\xbc$\x12I\xf2K\xb7\x97n\xed\xf9\xd3I\xdc\x9d\xf2\xba;\xe6\x8d\xb0\xc1C\xe2Z&\xc1oh\xfc\x9b\xf4H\xf2\xc4\x1d\xecW?|T[\x82\xb1\xbf\x12\rB\xb7\xee\x86\x93\xa9\x87\xc6\xab\x04\x9c<\xed\x1ba\x11MU\xd7Sa#\x16\\\xf1\x04u\x9f@\xeel\xaf\xe5dk\xc4\xee\xd6\x9cO\x96+\xb8\x8e\x93@\xa0\xf0.\t\xcdw(d\xdd\xce\x11\x96;\x80b\xe4X\xbd\x80\x96\xe9x\xc8\xcc\xbag\x1e^\xd1y\xddC\xddYk\xcb\r\xa2Q"~\xc2\xf6\xdd;1\xf2\xd8n\xd1\xacI$\x99\xf7IP\xf0\xb1\xde,\x19_\x04\xba\xf7e\xa40:\xc0_i\x0c#\xd9\x8dV\x03\xc4<K&\xb7\x048\xfb\xb7VX\xa3\x06\x07\xaf\xf9\x8d\xa5A\xae\xb7\x86[\x82\x9b\xd60\x00\x9c\xca\xc9\xb5\xb5\x00\x8b=\xf4\x07PK\x07\x08\xe9\xfa\x8cy\x18\x03\x00\x00\x05\x08\x00\x00PK\x03\x04\x14\x00\x08\x08\x08\x00\xbbY*X\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\r\x00\x00\x00xl/styles.xml\xcdW\xc9n\xdb0\x10\xbd\xf7+\x08\xfa\xdaF\xf2\x12[($\x05\xb0j\x17\xbd\xf4\x92\x14\xe8\x95\x96(\x89(\x17\x81\xa2\x03;__.\xb2\xac\xa4\x12\xe2\xc66\xe0\x13\xc9\xd1\x9b7o\xb8\xcc\xd8\xe1\xc3\x8eQ\xf0\x8ceM\x04\x8f\xe0\xf8\xce\x87\x00\xf3Td\x84\x17\x11\xfc\xf5\xb4\xfe\x12\xc0\x87\xf8SX\xab=\xc5\x8f%\xc6\nh\x07^G\xb0T\xaa\xfa\xeayuZb\x86\xea;Qa\xae\xbf\xe4B2\xa4\xf4R\x16^]I\x8c\xb2\xda81\xeaM|\x7f\xee1D8\x8cC\xbeek\xa6j\x90\x8a-W\x11\x9c\xb5&\xe0\x86\x1f\x99\x962\xbf\x87\xc0\xd1%"\xc3\x11\x1c}\x1e\x8d|\xad\xcf\xeb\x05\xcf{\xc1\xfe\x10|1\x00\x1f\xc2\x07Cx\xeb\xe05\t\xc5a.\xf81\xaf1t\x868\xac_\xc03\xa2\xda2v\t\xa4\x82\n\t\x08\xcf\xf0\x0ek\xfe\xc0FE\x0c;T\x82(\xd9Hb\x8c9b\x84\xee\x9dyb\x0cv\xbf\x1b\x1c#\\H\x1b\xdfE\xb1\x83\x11A(mE\xcc\xa13\xc4a\x85\x94\xc2\x92\xaf\xf5\x024\xf3\xa7}\xa5\xb3\xe1\x82cGcq\xef\xa03$\xff|\x97h\x7f\xbaG-(\xc9\xfe\x17\xaeaEb\xb7I\x16\x9b\x08.\x93$Y\xb9\xcd\xee8\x9eE\xb9\xf6W\xcbo\x8b\x8bR\xae&\xabd\x1d\x0cR\xdaA\x1f\xd0F\xc8L?\xb9\xc3\x11\x05\xf0`\x8aC\x8as\xa5\xdd%)J3*Qy\xe6\xa3R\x82\xe9IFP!8\xa2&\xc0\xc1\xe38\x1a\x10\xb0/5\x82\xaa\xd4/\xad\x0ff\x02\xbc\x05\x9d\xe1j\x85^\x9bO\xef\xc2\x05C|\\\xc7E3h\xc9\x9a\x89\xbe\x17)\xa6\xf4\xd1\xa0~\xe7\xaf\x8a\xc8.\xef\x14#\xdf\x94"\xdeN\xf5\x8dj\xa6\x8e\xc6-\x0c\x7f\x97\xcdqwig\x1f\xe2\x05\xbb\xbc\rp\x82\xf7\xb4\xeb=;z\x03TUto\xde\x86\xde\x0f\xb9\xc5\x8dai\xb1\x8d)\x0eu\x19,8\xc3\\\x99\x06\xa5Hjj^\xaa\x97\xd8\x15\xbd]~\x92\x86\xd9\x19\x1a\xdef9}\x7f\x8f\xfe\x89\xf0\x8a\xd0\x99~n\xd9\x06\xcb\xb5m(=\xd9\x96B\x92\x17\x1d\xc6\xe4koS\x7f\xbaM\x87\xbc\x159\xb3[\x12\xd3\xfc \xb8\x1d9\x8b\xdb\x92\x13\xdc\x92\x9c\xfew{\xdf\x15\xb38\xef\xdd\x9e\x10\xe1\xcc\xcapB\x84\xf9\xd5#L\xaf\x11a\xa8\x03\x0c\x1d\xb4\xe9\x89\xed9{M\xdb\xd1\xb3\xe3_\x88\xf8/PK\x07\x08R\xd0\x97\x88[\x02\x00\x00v\x0c\x00\x00PK\x03\x04\x14\x00\x08\x08\x08\x00\xbbY*X\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x0f\x00\x00\x00xl/workbook.xml\x8d\x8e\xbdN\xc30\x14\x85w\x9e\xc2\xba{k\xa7T\x08\xa28]\x10R7\x86\xc2\xee:7\x8d\xd5\xd8\x8el\xd32"\xc1\xca\x06#\x12+[%\xfaVm\xdf\x81$U l\x9d\xae\x8e\xeew~\x92\xc9\xa3.\xc9\n\x9dW\xd6p\x88\x86\x0c\x08\x1ai3e\x16\x1c\xeef7\x83K\x98\xa4g\xc9\xda\xba\xe5\xdc\xda%\xa9q\xe39\x14!T1\xa5^\x16\xa8\x85\x1f\xda\nM\xfd\xc9\xad\xd3"\xd4\xd2-\xa8\xaf\x1c\x8a\xcc\x17\x88A\x97t\xc4\xd8\x05\xd5B\x198&\xc4\xee\x94\x0c\x9b\xe7J\xe2\xb5\x95\x0f\x1aM8\x868,E\xa8\xc7\xfaBU\x1e\xd2\xdfe\xb7\x8ed"`t\xc5\xc6\x1crQz\x04\x9a&\xcd\xe7^\xe1\xda\xff\x81\x8d$B\x06\xb5\xc2\x99\x98s`\rG{`\xbb\xb9\xbb\xc4\x08\x8d\x1cv\xaf\x9b\xfd\xc7\xf6\xf0\xb2\x01\xe2b\x95qp\xd3\xec\x1cH\x8bLk\x195!\xff\x0c_o\x87\xe7\xa7\xfd\xe7\xfb\xee{\xdb\xf3\x8c{\x9eQ[\xdc\xb5\xd1n_\xfa\x03PK\x07\x08\xcf\x91\x1a\xbe\x01\x01\x00\x00\x94\x01\x00\x00PK\x03\x04\x14\x00\x08\x08\x08\x00\xbbY*X\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x1a\x00\x00\x00xl/_rels/workbook.xml.rels\xbd\x92\xc1j\xc30\x0c@\xef\xf9\n\xa3\xfb\xe2$\x1de\x8c8\xbd\x94A\xaf]\xf7\x01\xc6Q\xe2\xd0\xc46\x92\xbb\xb5\x7f?o\xd0\xad\x81\x8d\xeePv\x12\x92\xac\xa7g\xe3zu\x9cF\xf1\x8a\xc4\x83w\n\xca\xbc\x00\x81\xce\xf8vp\xbd\x82\x97\xdd\xd3\xdd\x03\xac\x9a,\xab\xb78\xea\x98\xce\xb0\x1d\x02\x8b4\xe4X\x81\x8d1<J\xc9\xc6\xe2\xa49\xf7\x01]\xeat\x9e&\x1dSJ\xbd\x0c\xda\xecu\x8f\xb2*\x8a\xa5\xa4K\x064\x99\x103\xac\xd8\xb4\nh\xd3\x96 v\xa7\x80\x7f\xc1\xfb\xae\x1b\x0c\xae\xbd9L\xe8\xe2\x0f[$[M\xd8>GJ\x17\xe2\x04\xd6\xd4cT0+\xe7\x89\n\xf2W\x9f\xea\xa6>\xf14\xe2\xa5\xc8g~\xc5`qK\x837O{\xb6\x88\xf1[\xe2\xab\xf4\xf1^)\x94W|\xee\xff\xd9\xa7:\xfb\xd4r\xf6\x0f\x9b\xec\x1dPK\x07\x08\xbci+\x8b\xdc\x00\x00\x00\xc0\x02\x00\x00PK\x03\x04\x14\x00\x08\x08\x08\x00\xbbY*X\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x18\x00\x00\x00xl/worksheets/sheet1.xml\x9d\x9dYs\x1c7\xb2\x85\xdf\xef\xaf`\xf0\xfd\x92\xd8\xabJ!ib\xcc\xa5W\xb2\xf7\x8do\xb4\xd5Z\xc2\x92\xe8 ik~\xfe\xa0)R\xa3\xae\xf3\xd1U\xe6<x\xac\xf6\xa9\xcc*\x1c \x81L\x1c@\xaf\xff\xf5\x9f/\x9f\x0f\xfe\xda\xde\xde}\xba\xf9\xfa\xe6\xd0\x1e\x99\xc3\x83\xed\xd7\xdfn\xde}\xfa\xfa\xe1\xcd\xe1b~\xfe\xff\xe5\xe1\xbf\xde\xfe\xdf\xebo7\xb7\xbf\xdf}\xdcn\xef\x0f2\xfe\xeb\xdd\x9b\xc3\x8f\xf7\xf7\x7f\xbc:>\xbe\xfb\xed\xe3\xf6\xcb\xf5\xdd\xd1\xcd\x1f\xdb\xaf\xf9\xbf\xbc\xbf\xb9\xfdr}\x9f\xffx\xfb\xe1\xf8\xee\x8f\xdb\xed\xf5\xbb\x87\x87\xbe|>v\xc6\xa4\xe3/\xd7\x9f\xbe\x1e\xbe}\xfd\xee\xd3\x97\xed\xd7\x9d\xc3\x83\xdb\xed\xfb7\x87\xff\xb6\x87\xc7o_?\x00\x97\x9f\xb6\xdf\xee~\xfa\xf7\x83\x9d\xdf_on~\xdf\xfd\xa1\xf7\xee\xcda~\xbd\xfb\xeb_g\xdb\xcf\xdb\xdf\xee\xb7\xf9\xcf\xf7\xb7\x7fnwO\x1f\xcb\xe3\xe7\x0f\xaf2\xbe=x\xb7}\x7f\xfd\xe7\xe7\xfb\xe9\xcd\xb7\xee\xf6\xd3\x87\x8f\xf7\xf9+c\xfe\xcc\xfc\xd0o7\x9f\xef\x1e\xfey\xf0\xe5\xd3\xee\xe3\x0f\x0f\xbe\\\xff\xe7\xe1\xff\xbf}zw\xff\xf1\xcdayT\x94\x95I.\x1e\x1e\xfc\xf6\xe7\xdd\xfd\xcd\x97\xd5\xf7\xdf\x9f\xbc\xfex\xd4=>\xea~<j\xedQa\xcb\xa2\xf1I\xff\xf8\xa4\xff\xdf\x93\xe1(\x852\xf8\xe6g\xc3\xe3\xb3\xe1\x9f\xbfp||4\xfe\xf3G\xd3\xe3\xa3\xe9\x9f?Z<>Z\xbc\xe0c\xcb\xc7g\xcb\x17<[=>[\xbd\xe0Yk\x9e\xba\x85\xf9\xf1\xb43G\xd1\x14\xa5m\xfeb\xfb\xa3S\xd9\x97<\xfd\xd4\xaf\xac{\xc9\xd3O}\xcb\xfa\x97<\xfd\xd4\xbb\xec\xff\xba\x97\x8bG\xbe\x15\xd1\xf6\xa9\x7f\xd9\xf8\x92&\x7f\xeab\xf6\x05}\xcc>u2[\xbc\xe0\xe1\xa7^f\xcb\x17<\xfc\xd4\xcd\xecK\xfa\x99{\xeag\xce\xbc\xe4\xe9\xa7~\xe6\xecK\x9e\xfe\x11\xbf^\xd2\xcf\xdcS?s/\xe9g\xee\xa9\x9f\xb9\xf0\x92\xa7\x9f:\x9a\x8b/y\xfa\xa9\xa3\xb9\xd4\xba\x8f\x1f\x7f\x9f5\x1e\xe6\x98\xd3\xeb\xfb\xeb\xb7\xafoo\xbe\x1d\xdc>L\x1b\xbb\xc9%{7O\x8f>M8\x0f\xcff\xbf;X\x9e\xed\x0e\xee\x1e\xd0\xf9?\xdc\xe5_\xffzk^\x1f\xff\xb53\xfc\x88\xf8E\x11v\x1fq\xf2\x1d\xe1~B\xb8}\xc4i#\xe2\xac\x11q\xde\x88\xe84"\xba\x8d\x88^#\xa2\xdf\x88\x184"\x86\x8d\x88\x8bF\xc4e#b\xa4\x08\xbf\x8f\x187"&\x8d\x88i#b\xd6\x88\x987"\x16\x8d\x88e#b\xd5\x88X7"6\x8d\x88\xab\xbfC\x1c\xe7\xd1\xf9c\x88\xba\x96C\xd4\x89\xc1\xfa\x10UD}\x88*"\xd4\x86\xa8"bm\x886"\xce\x1b\x11\x1dE\xa4\xda\x10mD\xf4\x1a\x11}E\x14\xb5!\xda\x88\x186".\x14Q\xd6\x86\xa8"\xaa\xda\x10m\xe4e\xdc\xd8\xa6\x93F\xc4\xb4\x111kl\xd3y#b\xd1\x88X6\xb6\xe9\xaa\x11\xb1nDl\x1ay\xb9\xfa;^\xf6\x86\xa8o9D}\xe3\x10UD}\x88\x02\xa2f\xe4\xb4\x19r\x06\x90\x9a\xa3s\x80\xd4g\xd2fG\xddfG\xbdfG\xfdfG\x83fG\xc3fG\x17\xcd\x8e.\x9b!\xa3f\xc8\xb8\x192i\xfe\xa2i\xf3\x17\xcd\x9a\x1d\xcd\x9b\x1d-\x9a\x1d-\x9b\x1d\xad\x9a\x1d\xad\x9b\x1dm\x9a\x1d]\xfd-do\xe8\x86\x96C7<X\xac~\xb6X\x9b\xd1\x7f\x01H\xed\xbdN\xbeC\xfc\x03\xe4\xeb\xf78\x1eRecyT\x1f\xc3\x8a\xacR\xac\xbcsu\xe4\x19\xd8L)T\xc6\xd4\x91\xe7\x80\xac\xaa\x10\xd4f\x07\xbc\x07\x1bL\xe5\xeb\xc8.\xd8\x8c\xb1H\xa6\xaa#{\x80,\xe3.\xd7\xab#\xfb\x8at!:\'&\x07\n\xb4\xa6p\x95\xad\x03\x87\x00\xf4E\xe5\xa5\xd9/\xbe\x03\xe3\xcf\xae\xeb\x98K0V\x98d\xd5\xda\x88\xe8.b\xe1R\x1d9F\xbace\xc5\xe6\x04\xe96!J\xebL\x91\xee\x08\xc8\x19\xd1m\x8a\x18\xa4\x0b\xcd\x91n\x17\x8d4\xf9\x02\xe9\x8e\xc6\x8b\xf7%\xd1]\xfaB>}\x85tW:x\xd6\x00\x0c&8\xf9\x9c\r\xd1]\x8bHWD\xb7\x8b\xe5\xde\'\xef\x85\x94\xd82\xa4\xc4\xef\x19\xf3^\xca\\[\xd6\xfd\x125\xa6\xd4\xd6d\'Q\xde/\xbf\x9e)$\xa2(\xceF\xeb\x83\xf0qF\x06\x83\x97\xa1\x7f\x0e\xb8\xb2\xb2\xd2\xb7;\xe08\xe4~ o\xd8U`*J/\xf1\xa1\x87_\x9c\xe4\x05\xfb\xe0\xd8TQ\x06\xf4@qy\x90H\xbb\x0c\xe1\xf5|\x14\xaf\x17Q\xfa\x94=\xaaj\xbc^\xaa-W\xfaR\x8c\x8d\xe8S\xbd\x91&\x19#\xb9\xd6I\x1bO\xc8\xa0\xd3\x19`\x8a\xe4\x9a(\xa1\x03\x1c\xfb<\xa9\x08pN\xe4&\'\xb8\x05~\xb1\x0e\xdd%8\xb6\xb6\x94\x0fY!\xb9\xd2.kx\xbdX\xca\xdbm\xda\x90{E\xe4&k\x9e\x8b\x17\xa9e\xbcH-\xe2E\xd2xQ\xcb\xaeN\x92~\xe9\xae\xdc*\xf1Bq6\xf3\x10\xa5\xef\x9d\x81A_\xc2\xf2\x03p\xa5S{\x1dp\xecl\x082\xc5t\x15\x18\xcb2\n\xb7=p\x9c\x03\xb8\xae;\xc0\xf1n^\x97x\xa1\xb8P\x05\x81\r\xe1\xf5R!\xcdr\x91\xa0K\x95\xb5\x02\xc0\xa5\xdar1\x06\x89\xb5#"\x17\x16cc"7\x8f\x1e\xe9\x05\x13"7%\xe1bJ\xe4\xda$m<cr5\xb0\xcc\x89\xdc\xa2\x94\xb9~A\xe4&+\x9d`I\xe4\xc2\x92`\x05\xe4\x96\x85\x90\xbb\x86\xd7\x8bA\xbcn\x90\xdcz\x9d\x81\xc8-|\xf1\\\xbc(Z\xc6\x8b\xa2E\xbc(4^\xd4\xea$\'\x05\xcd\xa2\xa9\x90>u\xaa\xc0]\xe0N\xba\xc0\x00\x8b\xd6\xf9J#\x06\x01\xc9u\x07\\\xe7E\x9a\xf6\x96.}L\xf0\x103\xf0\x1d+/\x1d\xbf\x0f\xc0P\x94\x82\x1b(\xae4\xba\xa8\x1a*,\x15A\xf3\x94\x82\xe6\xa1z]Qm\xe5U\x97\x0e\x8d\x112\xec\x93\xf01&\x863\xc12\xe9N\xa8\xf5r4\x90x5E\x86\x8d\x02g\xc4p\xb2\xba\x98\x9b\xd3\xc7\xe4\xe9H"\xd1\x82{\xa1\xb6\xf5\x92\x18\x86\xdch\xa5\xb8\xa2\x0c\xf2\x82k`8U\xe2u\xd3\x86\xe1+b\xb8*\xdcs\x91\xa3l\x199\xca\x16\x91\xa3\xd4\xc8Q{\xbb\x93\x12&\x19\x0f\t\xff\xa9\x02\xf3\x82)\xea\xecqF\x16\xcb\xc2J@8\x07`\x1e\x95\xba,\xed\x80\xeb\xa2\xf2\x85\xb8\xee\x92\xeb\x9c\xca\xcb\x08\xee\x91k\x9f\xa2F\x0e\x05\xe6/\x91n5\x807\xac*\x19 C\x85\x05[\xc9g\\\x94T\xe0\xa8\xaf6\xc0V\x19\xad\x18\x1b!\xc1yy \x81\x83\x08\xce\xf3\xa54\xde\x04\t\xb6\xba\x80\x99"\xc1A\x93\xc5\x19\x11\\\xc2;\xce\x91`\xab\xef\xb8@\x82\x9d\xf6\x84%\x11\\H\xf8]\x11\xc1\xfa~k \xc5Y\xe9U\x9b6\x04_\x11\xc1\xd5\xdet\xb9\x177\xaa\x96q\xa3j\x117*\x8d\x1b\xb5\xb7;\xa9\xb4\x81s\x84\xd4I\xfaT\x81>\x9aR\x17ugd\xb1\xc8\x0b\x04\x89\x1b\x00,\xbd\xd1\x19\xae\x03\xae}\xb2\xba\xd8\xe9\x92k[D\x89D=\x00\x169\xeb\xd14\x05\x80\x01\xe2\xcb@q9\xf1\xd5\x15\x87\xc2\xca\xa4\xc5\xb7\x8b\xaa\xcd\x8a\x03\\\xc6\xa0E\xcc\x11}k\x9e\xa5\x058&\x86m\xa5%\xcc\t5si\xa0*\x8a\x0cC\xb5|\x86\x0c\']H\xcc\x91\xe1R\xfb\xcc\x02\x19.\xac\x00\x97\x00\x8c^\xbb\xccJq\xb9\x0bjmCa\x95\xd5\xb6\xde\xb4a\xf8\n\x18\xce\xdd\xd9?\x179v\xfa\xc3v\x02#\xd3"v<\x82~\x0e\x1e\xae\xf6\x15\'\x8f\x98\xfdV\xde\xf5.\x89\x1e\x80\xcc\x8d\x9c\xb4\x01\xcf\xd0f^+\ns\xe7\x84,\xbd\xd7\xc4\xbaC\xde\xf3Tg\x05\xd9%\x9b\xbb\x8e(]\xb6\xc7\xefi\xb4\xb8\xd3Gd\x84\xda(\x00\x937\x82\x1b\x92A\xeb\xa2\x96;\x1e\x81\xb5nVK:/\xc9mL:\x05\x8c\xf0Cr\xc4\x96p8F\xba+\xab\xf5\x9d\t\xdb\xcc\x19\x91\x04\x13\xa4;\xc2$?#\xef!\xe7\x1b2\x0c\xe7Hw\x11\x92\x96>\xf8=\x0b\xad1-\x11Yj\xbfXQ\xbb\x83\xeb5\xd2\x1dt\xd7a\xd3\x8a\xee+r\x9bBx\xb6dj[\xeb\x16m\x9b\xb0b5\xac\x88x\xd1\x02\xd3E\xa5\x89\xfe) }Yy\xdd\x189#\x9b\x95\x81\xed\xc1sD\x96Q\xb3\x85\x0ez\x0f\xae\x90\x11\xdb\xc5/*\xf3\x92C\xc3\ny\xcf\xc1B\xbaE\x1f\x90\xd1\xa8\xc5\x01\x7f\x0fD\x15\xf8\x1a\x93\x04w\xf1\x88\xab\xadz\xeb\x9b.`\xadH\x95\xf6\xee\xd13d\xeb"a\x8c\xcd]T\x1a\x9b\'h\xb3*5\xd3\x9cr\xe3\x18M\tf\xe8=\xe7\xaf2\x0c\xe7\xfcE\xa5R\xb3@\xef\xa1\xd2\x8d\xae%\x91\r\xf9\xd2\np\xcexi\xa15}\r\x84\xe7M+\xb2\xaf\x88\xecLL\xf5lHi\xab\xb3\xb4\xaeMHq\x1aR\\=\xa48h\xe9\x9c\x9bBHQd\xce\x0cL\x92\xf8}\x06Hg\x8d\xd75\xcd9!w\xc5<\x19\r\x1d\xf2\xee*\xab\xab\x9f.\xd94y\xa5\xa2E\x12Bf\xf7\xba\xeb\xda\xa7V\xb2\x95f\xdb\x03zM\xef\xa4\x83\x0f\x01W\xc0v\xef\xc5#\xae&\x0e\x90\x85\nX++\r\xb7#f;\xe9\x949F\xb6\x8d)5\xe9\xc1\xf6\xae\nef\xcal{\xdd\xb1\x98!\xdbE*\xa4W\xce\xd1{tZd[ \xdb.\xe8D\xb7\xa4Vr\x90A\xae\xb0\x91`_\x97\xf8\xa9\x9c\x96M\x98\xedT\x0f*d\xad\xf0\xcf\x07\x15\xdf6\xa8\xf86A\xc5kP\xf1\xf5\xa0\xe2\xa1:\x90\xff\xa7\xc5\x13@\xba*\x16Z!8C\x9b\x0e\xaa\xa9\xe7\x84\x8c\xae\xd2\x95B\x07\xbd[\xaf\xc5\xe1.z\xb7\x90\x83\xf4\xd0{\x1e8\x82\xec\x032x\x1d`\x03\xb4\x08[\xbd\xf45\xa5\xeeb\\<\xe2jS\x97\x1c\xd9Pk\xa9\x88:\xff\x8f\x98l\xa7U\xa816w\x80m\x91\t\x93\xed\xb4\xb9\xa7LvR\xe4\x0c\xbd\x1b\xab\x15\x8d9z7Q\xa9Y \xb2\x82VZ\x12\xd9IuO+\xb2\x08\xdb\xffk\xc0\xe5P*~7Lv]^Fd\x97\xd6>\x1fR\xda*Vmh\x13RT\xb4\xeaB=\xa4\x90z\xb2\x84\n\xd2) w+\xb8\x12R\x1f\xb0\x19M\xa9\x85\xfesB&\x135\xc5\xee\x90\xf7<k\xaa\xf7.{7\xaab\xec!2\xdb\x84\x8a\n\xc9\x185c\x18\x00\xae4\xd2\x94Ct\xac5\xc5\x0bK\x12\xc6:\x85\x97`-\x95I\x03\xe3\x88\xb9v\x162\x1f\xe2\xdaX\xdd\xd2\x9epk[\x8d=S\xe6\xda\xa8\xf2w\x86\\\x17\x05(V\xc9f\xc83\x92*V\x99k\xd0\xcb.\x89k\xaf\xe2\xe8\x15q\xad+\xd75:\xd6*\xce\x86\xb9\x96\x83e\xc0u\x15\xcd\xb3\x9b;\xb6\xad^\xd5\xb6\x11\xacZU\xac\xba\xbab\xd5\x82@\xce\x16%\xa8\xe0\x01\x19M\x0e\xdeB\xde\x19\xd9\x0c\xce\xeav\xfe9!\x93\x0bJ_\x07\xbdS\xc7\xed\xa2w\xe3t\x9d\xd9#d\xacJ\x95~\xf6\x01\x19\xa2\x97\xb07\x00\\\xce\x8f\x047\xc4\xef\xd6\x81}aU\xe3\xe8\x8e|=\xa0\xa8\xb1*g\xa4\x10P\x88\xeb\x12\x12\xd21s\x1dt\xbcL\xb0\xb5iWh\xfa\x0c\xd7\xa0K\xa3\xd6\xae\xaaJ\x17\x1es\xe6\xda\xe8\xcau\xc1\\\x83\x8avI\xdea3{E\\C\x19w\x8d\x9e\xb5}6\xc8\xb5\xab\xef\x16\x13\xd9q\x7f\xa9\xbe\x1fQ\xda*Zm\x1bI\xabUM\xab\xabkZ-H\xe8v\xf3\x16d=\xa0\xefKEN|4\xa2\x80M\x97G\x16,Q\x00\x99\x87\xaa.E;\xe8\xdd;-\xbat\xd1\xbb\x81-\xa7\x1ez\xcf\x1f$\xde\xfb\x80\xdc\xc9\x854\xa2\x80V2\x96\x10Q\xc83E\x14UAR%E\xad\xe5\x84P\x89\x19!\xd9yA\xa8\xb2\x13j\xee\xc2B\xde1\xc1\xe6\xf6\xa0\x00\x9f\xe2G\x170]\xcf\xb8\xab\x19%{\xfeLW\xd35\xee\x82\xbb\x9a\xd5\x89fI\xde\x8dn\xa0\xad\x88\xec\xa4\x0b\xd25z\xd6,j\xc3dK!\x05\xc8v\xfb\xca\x8d\xfd\x90\xd2V\xf4j\xdb\xa8^\xad\xca^]]\xf6jQ\xd8\xe9\xb4fu\nH\xbf\x93\x9fB\xd6\x036\xf3\xcaL\'\xffsB\x16y\x8e\x81E\nx7\xa5\xd5#\x1d]\xfc\xa2\x04=\xb7G\xc8da{\xb8OH\x0f"\x89\x01\x00\x83\xd7\xe4z\x08\xb8\nN\xf4]X\xd5G\x82\x90\r\xac\xc5\xc2\xaa\x80x\xc4l{\xcdf\xc6\xcc\xb6W\x9b\x13f;\xa8Rg\xcalC\xd9l\x86l\x17p8b\xcel[\xd5J.\x98m8j\xbaD\xb6\xe1\x18\xe5\n\xd9\xf6\xaa\x85%\xb6\x93\x81RJ\x1b\xb6\xaf\x90\xed\xb4\xe7v?\xa8\xb4\xd5\xc3\xda6\x82X\xab\x8aXWW\xc4Z\xd0\x0e\xee\xe4\x8d\xb0NQ\xa4/\x92\xd3\t\xe9\x8cl\x16\xa1P\x89\xe89 \x9d\xc9)\x92\xea\xe9\xc9{\x8aV\xe5\x86]\xf4\x9e\xe3\x0fl\xf9\x00\xb2\n\xb0\xf6\xe9\x13\xd2Z\xdd)\x1d\xd0kV\x1aM\x87\x80+,\x05\x15\x14OJyV\xad\xc5TiN1b\xb6\xadVq\xc6\xc8vLzXv\xc2l{\x9d\x18\xa6\xcc\xb6\xd3~1C\xb6\x83\xd3\xc5\xc2\x9c\xd9\x06\x9b\x0bd\xdbWzrh\x89l{8\xc9\x07\xc0`tccMl{\xafg\xf9\x98m\xa9\xcf\x02\xdbEH\xcf\x07\x95\xb6bY\xdbF-kU.[O\xcfN,I\x1a-\xd4\xecN\x01\x99\xa7\x8e\xa8GB\xce\x00\x99\xd7\xd8Aw\x16\xce\t\xe9\xf2\n\x00\xb6|\xc0\xbb/\xe1\x08m\x17\xbd\x07\xab[S=\xf4\x1el)\xdf\xde\x07\xa4\x87\x03j\x03jM\xe8\xe0Clu\xd5\x92\\X\x95U\xba\xa3 1E\xad\xe5Q\xad\xa5\xeb\x11\x93mt\xc3y\x8cd\x9b\xa8ao\xc2\xcd\r\x11m\x8a\xcd\x1d\x93\xe6\xd93$\xbb\x08Zb\x9f\xa3\xf7\x1c\x7f@\xefF\xdes\x9c\x04i\n\x91\xadj\xa9\x15\xb5&(\xb5\xd6\xd8\xea:!mZ\x91}Ed\xc7\xf0|\xf2\xe3\xda\x8ah]\x1b\x11\xadS\x11\xad\xaf}\xc7\x89S=\x9e+B\xd44\xf3\x14\x90\xbbS1\x1a\xbe\xcf\xd8\xa6\xd3d\xfc\x9c\x90U\t\xa7\xd9;\xe4=\xa5R\xe7\xcd.z\xb7\xa5.r{\xe8=\x1a\r\x92}\xf2\x0e\xbb\xc3\x03\xb2\x08\x17*\x0c\x01\xe7-\xd4S\x9cj*\xf3g\xd4R\xecK\xb0VUI\x07\xc1\xe8\x19b4\xb7\x1fcsW\x95\x9e\x16\x98\xa0M\x07\xd3\xff\x14\x9b\xbb\x80\xe6\x9e\xa1\xf7\xdc\x8eZ\xa2E\xef&h\xe8[\xa0w_\xc2)@\xf4\xae\xbeWH\xb6\xe6\xd8k$[\xe7\xd7M+\xb2\xaf\xc0\x9a5f\xbfX\xb9\x1fS\xda*h]\x1b\x05\xadS\x05\xad\x97\xbb\x05I\x82\x18\xe0\x10\xfb) \xf3\xe4_h\xbc=\x03\xa4\xcbS\x1c\x14T\x08\x99s\n\xed\x12\x1d@\xfa*\x94:^\xbb\xf4E\x95/\xb4B\xdc\xc3\xf7\xb4\xb0\x93\xd4\'\xa4\xab4\xbf\x1f\x90s\x07\xd7)\x0c\th\x92\x96s/\x1c\xe9*\xebT_\x82\xb9\xa2\xb4*\xf4\x1f1\xdf\x01v~\x90\xefT\xeaQ\x88\t\xf3\x1dT\x089e\xbe\xbd\xbe\xe7\x0c\xf9\xf6P\xe7\x9f3\xdfN\xf7-\x17\xf8\x9e\x06DtK\xe4\xbb\xd4\x85\xdf\x8a\xf9V\xf9\xe0\x9a\xf9\xd6\xfa\xd4\xa6\x15\xdfW\xc4w\xb5\xcf\xf7~`i}_i\x1b\x1d\xadS\x1d\xad\xaf\xebh\x1di\x06M\xd4Y\xe9\x14\x90.\xaf@\xe0\xc4\x0f\xdatAU\x03\xe7\xec\xdd(\x83\x1d\xf2\xee*\xa7\x99i\x97l\xda\xe85\x0b\xe9!\xd2\xc3\x02\xa8OH\x0f5\xb9\x01\x00\x0b\xa3u\xa7!\xe0\x92\xd3t\xfc\xc2\xa9\xb2\xd2\x1e\xd5\x0b\xb5`,\xe4%\x88\xee\'ss\'\x1d\xd7c&\xdb\xe86\xd1\x84\xc9\xb6\xda6Sl\xee\xdd\x1dz\x1aU\x88\xec2i\x05m\xced\x1b\xad\xaa.\x98\xec\xa8i\xf1\x12\xc9\x860\xb9B\xb2\xf5\xd8\xf8\x9a\xc8\xf6\x1a\xce6Hv\xfd6\x9b+d\xbb\xa8\x9e=E\xb8\xbb\xa3\xbd]L\xf1mb\x8a\xd7\x98R\x97\xd1>b\xf6C\xbd\t\x9a\xd6\x9c\x02\xd2W%\\\xfcp\x866\x13\xdc\xb3q\x0eHg\x1cx\xef\x90\xf7"YJ\x80\xc0{\x19\x92N\xb2=|O\x1bUJ\xdf\xa7\xf7tp\xd4r@&m\xa1\x02\xe2!\x01\r\\\xf1w\xf1\x08l8F\x08\xe6Rnq\x08+\xc8w\xa1\xf9\xca\x98\xf9v\x9aWM\xd0f\x84s\xec\xd3g\xf8\xd6%\xe7\x0c\xf9\x8eQg\x9b9\xf3mU,\xb2`\xbe\x83\x86\xaa\xe53|\xabN\x05\xf9\x06\xbd\xe1\x1a\x81F\xb3\xf2M+\xbe\xaf\x90\xefj\xef\x9b\xf7\x03K[1\xadk#\xa6u*\xa6\xf5u1\xad\x03\xad_Y\x18\xd0\xbe\x01\xd2W\xb4\x05|\x866K\x0fW\xb0\x012w4\xa8Yt\xc8{i\x12l+\xa3\xf7`5\n\xf4\x08Y\xa5\xa8[\x92}B\x92\x9cn\x00\xc0`u;m\x08\xb8d4\xa2]8\xbc\x00\xb6\xae~#k\x15\xe4\x16#f\xdb\xeai\xa31\xb3\r\x82\xd6\t\xb3Mw8>\xc3\xb6\x9e\xa6\x991\xdbV\xb3\xc99z\xf7\xa5\x16\xdc\x17\xcc\xb6\xd3,~\x89lCL[\x11\xdb \x89Z\x13?N\x17i\x9bVl_!\xdb\xfbk\xbe\xfd\xa0\xd2VP\xeb\xda\x08j\x9d\nj}]P\xeb\xf0\xc6\xc9\x04\x87~\x00\x19w3\x8d^\x99B6wB#(\xd7\xb6Ev\x00\x19v\xc3\x01\x82\n\x89,\xa3\xd1\xfc\xab\x87H\x07[\xd5}@\xe6U\x8d\xae\xb3\x07\x00\xccK*\xa8\xac\x80.\x12j:\x17\x8en\r\x95\xa5\x8a\x1a\xcbi\x9c\xe6 #&\xdb\xe8bj\x8cd\xe7\x81\xa5\xf7\xb5!\x85t!\xcb\x94\x91p\xd3\xd4\x0c\xc9\x8eVw\xaa\xe6Ha\x0e\xe3\x90\x01\xa1\xf6\xb6\xa2\x98Bd\x838q\x85d\xebjnMd;\xb8R\xba\r\xd9WD\xf6\xee\xd6\xf6gCJ[E\xadk\xa3\xa8u\xaa\xa8\xf5uE\xad#Y \xdd8p\n\xc8`J\xd8\x95;#\x9bU(Uy\x7f\x0e\xc8\x9d\x9eWS\xa5\x0ez\x0fp\xc9\\\x17\xbd\xbbRg\xed\x1ez\xb7I#@\x9f\xbc\xc3\xf1\xbb\x01Y\x84c\xd1C\xc2\x81\xe4\xf0\xc2\x91\xc8R*\xb5x1\xaf\xae\x13F\xc8u\x9e\xaa!\xf9\x81\xef\xb59M\x82\x9a\n\xb5v,\xb4\xffL\xb1\xb5C\xa5u\x9a\x19r\x9d\x02\\\xc9\x84\xde=d\xa6\x0b\xf4\x9es\x1aH~\xc0;\xa4\x92+\xe4Z\xcb\xedk\xe4Z\xd3\xb3M\x1b\xae\xaf\x88\xeb\xb0\x9f\xbe\xee\x07\x94\xb6zZ\xd7FO\xebTO\xeb\xebzZG\x97\x87\xeeV\xcd\x1aP@\xc6\x17\x9c\xd7\xed\xdf3\xb2\x19s\xea*\x1d\xe2\x9c\x90\xa5\xb7\xbaQ\xd4!\xef\xbe\xc0\x8a\nyO\x85\x1e\xd1\xe9\xa1w\x13\xf4\xb8L\x9f\xbc\xc3I\xcb\x01Y\x84\xebw\x86\x84\x83\xdbi/\x1c\t,\xeb\x9a\xe8K\xb0\xb6\xbb[F\xcc\x8d\x90\xec\x12\x16\xa4c$;\x81\xe2wB6\x93\xb5*U\x9fbsG {\x86\xdeM\x05\xca7${wI\xa9F\x14\xf2\xee<E\x14\xeajp\xea\x07\xc9V1\xf6\x1a=\xebjo\xd3\x8a\xec+";\xba\xe7/\x89tm\xd5\xb4\xae\x8d\x9a\xd6\xa9\x9a\xd6\xd7\xd5\xb4\x0e\xaf\x15-t\xddx\n\xc8\x94\\\xa9k\xd13@\xe60\x01k\xe6sB\x86\x00\x85\xc0\x0ey\x8f\x85\xd5\x1e\xd1E\xef\xc9\xe8\xb2\xb5\x87\xde-\xdc{\xd6\x07d,t\xcc\x0e\xa85\xa3\xb6\xd0\x10\xdf\x11\xeeOq$\xaf\x94J\n\x88?M\x82\xd3\xb9#$;Y=\x1e8F\xb2m\xa4\xedd\xfa\x94\xa2\xd0|t\x8a\xcd\xed\xe1\xfa\xdf\x19\x92\x1d\xe1Z\xad9z\x8fN\xabR\x0b\xf4n"\xfc\xad\x18-\xc9^!\xd9p\xcf\x1byv\xba\x8c\xda \xd9r\x90\x10\xd9.\x92y\xbe>\xdbVL\xeb\x1e\xc5\xb4\xeeoc\xca#\xc8\xfc\x1cT\xeajZ\x07"C\xe3\n\xad\xc5\x9f\x022$o\xf4x\xe7\x19\xd9t\x19\n\x89\x0f }*t\xa5\xd0!\xef\xb1\x84{\xba\xba\xe8=\x80\x1a\xaa\x87\xdeC\xd0En\x9f\xbc\x1b\xb8>\x12p;m\xb2\x06\x15|G\x90\xbe\x91\xc0\xb2~\x1c\xf4\x12\xac\x95t\x89\xc8\x08\xc9\xf6V\x07\xd6\x18\xc9\xde\x9d\xf2\xd4\xa0\x82\x1a\xd9R3\x80)\x93\rW\x98\xcc\x98l\xaf\xbb\xf2\xf3g\x1a\x92\xb6}\x90l\xafs\xcd\x12\xc9\xd6`\xb1jI\xf6\x9a[HO\xfd\xb4"\xfb\x8a\xc8\xb6t\'\xd3\xf1O\x7f\xfd\xf5\x97\xed\xed\x87\xed\xc9\xf6\xf3\xeeo\xc5\xfe\xf1\xef\x07\xb7\xdb\xf7;a\xfe\xab\x7f\xfb\xdd_\x9a]\xfb\xfd\x17\xfb\xea\x17\xfa\xfd\xc4\xbe\xba\xb4\xf0\xfb\xc8\xbe\xba\xa2\xdfO\xdd\xabs\x07\xbfw\xdc\xab\x1e\xfd\xdew\xaf\x86\xf4\xfb\xd8\xbd\x9a\xd2\xef3\xf7jA\xbf/\xdd\xab\xf5\xc3\xef\xc7?\x7f\xfc\x1f\xd7\x1f\xb6\x17\xd7\xb7\x1f>}\xbd;\xf8\xf5\xe6>\xc7\xd77\x87\xe6h\xf7\xb7\xaf\xbf\xbf\xb9\xb9\xdf\xde\xee\xfe\x94\x9b\xf6\xe3\xf6\xfa\xdd\x8f?|\xde\xbe\xbf\x7f@\x1d\x1e\xdc~\x8f\xc5\x0f\xff~\x7f\xf3\xc7\xe3\xb3;\'\xdfnn\x7f\x7fh\xf0\xb7\xff\x05PK\x07\x08\xc1d\xa0V \x14\x00\x00\xaf\x83\x00\x00PK\x03\x04\x14\x00\x08\x08\x08\x00\xbbY*X\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x18\x00\x00\x00xl/worksheets/sheet2.xml\x8d\x94\xcbn\x84 \x14\x86\xf7}\n\xc2\xbe\xe2]\xa7Q\'m\x9aI\xbbh\xd24m\xf7\x8c\xe2%\xa3b\x80\x8e}\xfc\xa2\x9d\xd0\x89\x1e\x93\xee@\xbe\xff\x03\x04N\xb2\xff\xeeZtfB6\xbcO\xb1c\xd9\x18\xb1>\xe7E\xd3W)\xfex?\xdc\xc6x\x9f\xdd$#\x17\'Y3\xa6\x90\xe6{\x99\xe2Z\xa9\xe1\x8e\x10\x99\xd7\xac\xa3\xd2\xe2\x03\xeb\xf5H\xc9EG\x95\xee\x8a\x8a\xc8A0Z\xcc\xa1\xae%\xaem\x87\xa4\xa3M\x8f\xb3\xa4h:\xd6O\x13"\xc1\xca\x14\xdf;\x98d\xc9\x0c~6l\x94Wm4\xcd{\xe4\xfc4u\x9e\x8b\x14\xdb\x13JV\xeca\x9e\xf7U\xa0\x82\x95\xf4\xabUo||bMU+\xbd\xa5\xc0\xb2\x8d\xff\x91*\x9a%\x82\x8fH\xe8\x11\xbd\x94|j\xe8\x05 \xbd%\xc7\xc3H\x07\xa4\xfe|\xce|;!g=U~a\x1e \xc61\x0c\xd1N#v\x8d\xd8\x05B\xeeB\x0c1\x1e,\xf6\x8c\xd8\x03B\xfeB\x0c1\x01,\xf6\x8d\xd8\x07B\xe1B\x0c1\x11,\x0e\x8c8\x00B\xf1B\x0c1;X\x1c\x1aq\xb8\x0e\x05\xcb\xc3\x83\x98\x8d\xc3\x8b\x8c8Z\x87\x9c\xe5?\x06\x98\xc0\x85\xc5\xb1\x11\xc7@h\xe3\xc4w&\xb4\x03B\xcb\xd5@\xcc\xc6\x89;\xf6\xdf\xed\xb7\xff\xb1Q\x08\n\xc2\r\xf7\xd5\xcb\x02\x9e\xcd\xda\r@\xc1\xf2B\x91\xab\x07<\xd0\x8a\xbdPQ5\xbdDG\xae\x14\xefte\xb0"}}J\xce\x15\x13SO\xcbj]\x7fL\xa7e\xa5\x9a)\x8c\xc4oe\x98\xdb\x8a\x0f\x97\xecTXL\x99\xcb~\x00PK\x07\x08\t\x86w\xa1\x8c\x01\x00\x00\x19\x05\x00\x00PK\x01\x02\x14\x00\x14\x00\x08\x08\x08\x00\xbbY*X\xfdV\x0fr\x04\x01\x00\x00\xf4\x01\x00\x00\x11\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00docProps/core.xmlPK\x01\x02\x14\x00\x14\x00\x08\x08\x08\x00\xbbY*XW(^#\xe3\x00\x00\x00F\x02\x00\x00\x0b\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00C\x01\x00\x00_rels/.relsPK\x01\x02\x14\x00\x14\x00\x08\x08\x08\x00\xbbY*X\xfep\x9e\x84=\x01\x00\x00\xb3\x04\x00\x00\x13\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00_\x02\x00\x00[Content_Types].xmlPK\x01\x02\x14\x00\x14\x00\x08\x08\x08\x00\xbbY*X\xe1|w\xd8\x91\x00\x00\x00\xb7\x00\x00\x00\x10\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\xdd\x03\x00\x00docProps/app.xmlPK\x01\x02\x14\x00\x14\x00\x08\x08\x08\x00\xbbY*X\xe9\xfa\x8cy\x18\x03\x00\x00\x05\x08\x00\x00\x14\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\xac\x04\x00\x00xl/sharedStrings.xmlPK\x01\x02\x14\x00\x14\x00\x08\x08\x08\x00\xbbY*XR\xd0\x97\x88[\x02\x00\x00v\x0c\x00\x00\r\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x06\x08\x00\x00xl/styles.xmlPK\x01\x02\x14\x00\x14\x00\x08\x08\x08\x00\xbbY*X\xcf\x91\x1a\xbe\x01\x01\x00\x00\x94\x01\x00\x00\x0f\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x9c\n\x00\x00xl/workbook.xmlPK\x01\x02\x14\x00\x14\x00\x08\x08\x08\x00\xbbY*X\xbci+\x8b\xdc\x00\x00\x00\xc0\x02\x00\x00\x1a\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\xda\x0b\x00\x00xl/_rels/workbook.xml.relsPK\x01\x02\x14\x00\x14\x00\x08\x08\x08\x00\xbbY*X\xc1d\xa0V \x14\x00\x00\xaf\x83\x00\x00\x18\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\xfe\x0c\x00\x00xl/worksheets/sheet1.xmlPK\x01\x02\x14\x00\x14\x00\x08\x08\x08\x00\xbbY*X\t\x86w\xa1\x8c\x01\x00\x00\x19\x05\x00\x00\x18\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00d!\x00\x00xl/worksheets/sheet2.xmlPK\x05\x06\x00\x00\x00\x00\n\x00\n\x00\x85\x02\x00\x006#\x00\x00\x00\x00'}




```python
os.path.realpath("population_240101.xlsx")
```




    '/content/population_240101.xlsx'




```python
#file_path="/content/population_240101.xlsx"
population = pd.read_excel("population_240101.xlsx",
                           header = 2,
                           usecols = "B,D,G,J,N")
```

    /usr/local/lib/python3.10/dist-packages/openpyxl/styles/stylesheet.py:226: UserWarning: Workbook contains no default style, apply openpyxl's default
      warn("Workbook contains no default style, apply openpyxl's default")



```python
population.head()
```





  <div id="df-d2d398bf-042e-4ca0-9024-c6a154a5886e" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>동별(2)</th>
      <th>소계.1</th>
      <th>소계.2</th>
      <th>소계.3</th>
      <th>소계.5</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>소계</td>
      <td>9659322</td>
      <td>9414093</td>
      <td>245229</td>
      <td>1706138</td>
    </tr>
    <tr>
      <th>1</th>
      <td>종로구</td>
      <td>151349</td>
      <td>140397</td>
      <td>10952</td>
      <td>28383</td>
    </tr>
    <tr>
      <th>2</th>
      <td>중구</td>
      <td>132054</td>
      <td>121441</td>
      <td>10613</td>
      <td>25546</td>
    </tr>
    <tr>
      <th>3</th>
      <td>용산구</td>
      <td>232069</td>
      <td>217288</td>
      <td>14781</td>
      <td>39717</td>
    </tr>
    <tr>
      <th>4</th>
      <td>성동구</td>
      <td>286564</td>
      <td>279374</td>
      <td>7190</td>
      <td>48514</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-d2d398bf-042e-4ca0-9024-c6a154a5886e')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-d2d398bf-042e-4ca0-9024-c6a154a5886e button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-d2d398bf-042e-4ca0-9024-c6a154a5886e');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


<div id="df-3958e5dc-a8b2-45b9-9a56-eae010bf5dd5">
  <button class="colab-df-quickchart" onclick="quickchart('df-3958e5dc-a8b2-45b9-9a56-eae010bf5dd5')"
            title="Suggest charts"
            style="display:none;">

<svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
     width="24px">
    <g>
        <path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zM9 17H7v-7h2v7zm4 0h-2V7h2v10zm4 0h-2v-4h2v4z"/>
    </g>
</svg>
  </button>

<style>
  .colab-df-quickchart {
      --bg-color: #E8F0FE;
      --fill-color: #1967D2;
      --hover-bg-color: #E2EBFA;
      --hover-fill-color: #174EA6;
      --disabled-fill-color: #AAA;
      --disabled-bg-color: #DDD;
  }

  [theme=dark] .colab-df-quickchart {
      --bg-color: #3B4455;
      --fill-color: #D2E3FC;
      --hover-bg-color: #434B5C;
      --hover-fill-color: #FFFFFF;
      --disabled-bg-color: #3B4455;
      --disabled-fill-color: #666;
  }

  .colab-df-quickchart {
    background-color: var(--bg-color);
    border: none;
    border-radius: 50%;
    cursor: pointer;
    display: none;
    fill: var(--fill-color);
    height: 32px;
    padding: 0;
    width: 32px;
  }

  .colab-df-quickchart:hover {
    background-color: var(--hover-bg-color);
    box-shadow: 0 1px 2px rgba(60, 64, 67, 0.3), 0 1px 3px 1px rgba(60, 64, 67, 0.15);
    fill: var(--button-hover-fill-color);
  }

  .colab-df-quickchart-complete:disabled,
  .colab-df-quickchart-complete:disabled:hover {
    background-color: var(--disabled-bg-color);
    fill: var(--disabled-fill-color);
    box-shadow: none;
  }

  .colab-df-spinner {
    border: 2px solid var(--fill-color);
    border-color: transparent;
    border-bottom-color: var(--fill-color);
    animation:
      spin 1s steps(1) infinite;
  }

  @keyframes spin {
    0% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
      border-left-color: var(--fill-color);
    }
    20% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    30% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
      border-right-color: var(--fill-color);
    }
    40% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    60% {
      border-color: transparent;
      border-right-color: var(--fill-color);
    }
    80% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-bottom-color: var(--fill-color);
    }
    90% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
    }
  }
</style>

  <script>
    async function quickchart(key) {
      const quickchartButtonEl =
        document.querySelector('#' + key + ' button');
      quickchartButtonEl.disabled = true;  // To prevent multiple clicks.
      quickchartButtonEl.classList.add('colab-df-spinner');
      try {
        const charts = await google.colab.kernel.invokeFunction(
            'suggestCharts', [key], {});
      } catch (error) {
        console.error('Error during call to suggestCharts:', error);
      }
      quickchartButtonEl.classList.remove('colab-df-spinner');
      quickchartButtonEl.classList.add('colab-df-quickchart-complete');
    }
    (() => {
      let quickchartButtonEl =
        document.querySelector('#df-3958e5dc-a8b2-45b9-9a56-eae010bf5dd5 button');
      quickchartButtonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';
    })();
  </script>
</div>

    </div>
  </div>





```python
population.rename(columns={population.columns[0] : '구별',
                           population.columns[1] : "인구수",
                           population.columns[2] : "한국인",
                           population.columns[3] : "외국인",
                           population.columns[4] : "고령자"}, inplace=True)
```


```python
population.head()
```





  <div id="df-447f8c47-c0c9-4512-9131-69c73262ac0c" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>구별</th>
      <th>인구수</th>
      <th>한국인</th>
      <th>외국인</th>
      <th>고령자</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>소계</td>
      <td>9659322</td>
      <td>9414093</td>
      <td>245229</td>
      <td>1706138</td>
    </tr>
    <tr>
      <th>1</th>
      <td>종로구</td>
      <td>151349</td>
      <td>140397</td>
      <td>10952</td>
      <td>28383</td>
    </tr>
    <tr>
      <th>2</th>
      <td>중구</td>
      <td>132054</td>
      <td>121441</td>
      <td>10613</td>
      <td>25546</td>
    </tr>
    <tr>
      <th>3</th>
      <td>용산구</td>
      <td>232069</td>
      <td>217288</td>
      <td>14781</td>
      <td>39717</td>
    </tr>
    <tr>
      <th>4</th>
      <td>성동구</td>
      <td>286564</td>
      <td>279374</td>
      <td>7190</td>
      <td>48514</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-447f8c47-c0c9-4512-9131-69c73262ac0c')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-447f8c47-c0c9-4512-9131-69c73262ac0c button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-447f8c47-c0c9-4512-9131-69c73262ac0c');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


<div id="df-bc4eab73-3e74-4758-9157-5ddc749a0490">
  <button class="colab-df-quickchart" onclick="quickchart('df-bc4eab73-3e74-4758-9157-5ddc749a0490')"
            title="Suggest charts"
            style="display:none;">

<svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
     width="24px">
    <g>
        <path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zM9 17H7v-7h2v7zm4 0h-2V7h2v10zm4 0h-2v-4h2v4z"/>
    </g>
</svg>
  </button>

<style>
  .colab-df-quickchart {
      --bg-color: #E8F0FE;
      --fill-color: #1967D2;
      --hover-bg-color: #E2EBFA;
      --hover-fill-color: #174EA6;
      --disabled-fill-color: #AAA;
      --disabled-bg-color: #DDD;
  }

  [theme=dark] .colab-df-quickchart {
      --bg-color: #3B4455;
      --fill-color: #D2E3FC;
      --hover-bg-color: #434B5C;
      --hover-fill-color: #FFFFFF;
      --disabled-bg-color: #3B4455;
      --disabled-fill-color: #666;
  }

  .colab-df-quickchart {
    background-color: var(--bg-color);
    border: none;
    border-radius: 50%;
    cursor: pointer;
    display: none;
    fill: var(--fill-color);
    height: 32px;
    padding: 0;
    width: 32px;
  }

  .colab-df-quickchart:hover {
    background-color: var(--hover-bg-color);
    box-shadow: 0 1px 2px rgba(60, 64, 67, 0.3), 0 1px 3px 1px rgba(60, 64, 67, 0.15);
    fill: var(--button-hover-fill-color);
  }

  .colab-df-quickchart-complete:disabled,
  .colab-df-quickchart-complete:disabled:hover {
    background-color: var(--disabled-bg-color);
    fill: var(--disabled-fill-color);
    box-shadow: none;
  }

  .colab-df-spinner {
    border: 2px solid var(--fill-color);
    border-color: transparent;
    border-bottom-color: var(--fill-color);
    animation:
      spin 1s steps(1) infinite;
  }

  @keyframes spin {
    0% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
      border-left-color: var(--fill-color);
    }
    20% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    30% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
      border-right-color: var(--fill-color);
    }
    40% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    60% {
      border-color: transparent;
      border-right-color: var(--fill-color);
    }
    80% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-bottom-color: var(--fill-color);
    }
    90% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
    }
  }
</style>

  <script>
    async function quickchart(key) {
      const quickchartButtonEl =
        document.querySelector('#' + key + ' button');
      quickchartButtonEl.disabled = true;  // To prevent multiple clicks.
      quickchartButtonEl.classList.add('colab-df-spinner');
      try {
        const charts = await google.colab.kernel.invokeFunction(
            'suggestCharts', [key], {});
      } catch (error) {
        console.error('Error during call to suggestCharts:', error);
      }
      quickchartButtonEl.classList.remove('colab-df-spinner');
      quickchartButtonEl.classList.add('colab-df-quickchart-complete');
    }
    (() => {
      let quickchartButtonEl =
        document.querySelector('#df-bc4eab73-3e74-4758-9157-5ddc749a0490 button');
      quickchartButtonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';
    })();
  </script>
</div>

    </div>
  </div>





```python
population
```





  <div id="df-b10d2f01-55f6-4a07-ac99-2b3df535d392" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>구별</th>
      <th>인구수</th>
      <th>한국인</th>
      <th>외국인</th>
      <th>고령자</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>소계</td>
      <td>9659322</td>
      <td>9414093</td>
      <td>245229</td>
      <td>1706138</td>
    </tr>
    <tr>
      <th>1</th>
      <td>종로구</td>
      <td>151349</td>
      <td>140397</td>
      <td>10952</td>
      <td>28383</td>
    </tr>
    <tr>
      <th>2</th>
      <td>중구</td>
      <td>132054</td>
      <td>121441</td>
      <td>10613</td>
      <td>25546</td>
    </tr>
    <tr>
      <th>3</th>
      <td>용산구</td>
      <td>232069</td>
      <td>217288</td>
      <td>14781</td>
      <td>39717</td>
    </tr>
    <tr>
      <th>4</th>
      <td>성동구</td>
      <td>286564</td>
      <td>279374</td>
      <td>7190</td>
      <td>48514</td>
    </tr>
    <tr>
      <th>5</th>
      <td>광진구</td>
      <td>350820</td>
      <td>336169</td>
      <td>14651</td>
      <td>55418</td>
    </tr>
    <tr>
      <th>6</th>
      <td>동대문구</td>
      <td>355667</td>
      <td>338115</td>
      <td>17552</td>
      <td>65657</td>
    </tr>
    <tr>
      <th>7</th>
      <td>중랑구</td>
      <td>389316</td>
      <td>384272</td>
      <td>5044</td>
      <td>76968</td>
    </tr>
    <tr>
      <th>8</th>
      <td>성북구</td>
      <td>441065</td>
      <td>429142</td>
      <td>11923</td>
      <td>78972</td>
    </tr>
    <tr>
      <th>9</th>
      <td>강북구</td>
      <td>295773</td>
      <td>291384</td>
      <td>4389</td>
      <td>67566</td>
    </tr>
    <tr>
      <th>10</th>
      <td>도봉구</td>
      <td>311386</td>
      <td>308986</td>
      <td>2400</td>
      <td>68682</td>
    </tr>
    <tr>
      <th>11</th>
      <td>노원구</td>
      <td>504641</td>
      <td>500104</td>
      <td>4537</td>
      <td>94102</td>
    </tr>
    <tr>
      <th>12</th>
      <td>은평구</td>
      <td>467295</td>
      <td>463298</td>
      <td>3997</td>
      <td>91395</td>
    </tr>
    <tr>
      <th>13</th>
      <td>서대문구</td>
      <td>321726</td>
      <td>308122</td>
      <td>13604</td>
      <td>57179</td>
    </tr>
    <tr>
      <th>14</th>
      <td>마포구</td>
      <td>376285</td>
      <td>365111</td>
      <td>11174</td>
      <td>56964</td>
    </tr>
    <tr>
      <th>15</th>
      <td>양천구</td>
      <td>441517</td>
      <td>438353</td>
      <td>3164</td>
      <td>74386</td>
    </tr>
    <tr>
      <th>16</th>
      <td>강서구</td>
      <td>572382</td>
      <td>566839</td>
      <td>5543</td>
      <td>99690</td>
    </tr>
    <tr>
      <th>17</th>
      <td>구로구</td>
      <td>417774</td>
      <td>394833</td>
      <td>22941</td>
      <td>78150</td>
    </tr>
    <tr>
      <th>18</th>
      <td>금천구</td>
      <td>242567</td>
      <td>229235</td>
      <td>13332</td>
      <td>44242</td>
    </tr>
    <tr>
      <th>19</th>
      <td>영등포구</td>
      <td>398842</td>
      <td>376139</td>
      <td>22703</td>
      <td>66362</td>
    </tr>
    <tr>
      <th>20</th>
      <td>동작구</td>
      <td>390843</td>
      <td>380622</td>
      <td>10221</td>
      <td>69350</td>
    </tr>
    <tr>
      <th>21</th>
      <td>관악구</td>
      <td>502430</td>
      <td>487192</td>
      <td>15238</td>
      <td>83932</td>
    </tr>
    <tr>
      <th>22</th>
      <td>서초구</td>
      <td>408753</td>
      <td>404484</td>
      <td>4269</td>
      <td>63822</td>
    </tr>
    <tr>
      <th>23</th>
      <td>강남구</td>
      <td>542327</td>
      <td>537139</td>
      <td>5188</td>
      <td>84271</td>
    </tr>
    <tr>
      <th>24</th>
      <td>송파구</td>
      <td>662872</td>
      <td>657101</td>
      <td>5771</td>
      <td>106281</td>
    </tr>
    <tr>
      <th>25</th>
      <td>강동구</td>
      <td>463005</td>
      <td>458953</td>
      <td>4052</td>
      <td>80589</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-b10d2f01-55f6-4a07-ac99-2b3df535d392')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-b10d2f01-55f6-4a07-ac99-2b3df535d392 button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-b10d2f01-55f6-4a07-ac99-2b3df535d392');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


<div id="df-39b00ade-38df-4d9a-a399-a0958342bef9">
  <button class="colab-df-quickchart" onclick="quickchart('df-39b00ade-38df-4d9a-a399-a0958342bef9')"
            title="Suggest charts"
            style="display:none;">

<svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
     width="24px">
    <g>
        <path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zM9 17H7v-7h2v7zm4 0h-2V7h2v10zm4 0h-2v-4h2v4z"/>
    </g>
</svg>
  </button>

<style>
  .colab-df-quickchart {
      --bg-color: #E8F0FE;
      --fill-color: #1967D2;
      --hover-bg-color: #E2EBFA;
      --hover-fill-color: #174EA6;
      --disabled-fill-color: #AAA;
      --disabled-bg-color: #DDD;
  }

  [theme=dark] .colab-df-quickchart {
      --bg-color: #3B4455;
      --fill-color: #D2E3FC;
      --hover-bg-color: #434B5C;
      --hover-fill-color: #FFFFFF;
      --disabled-bg-color: #3B4455;
      --disabled-fill-color: #666;
  }

  .colab-df-quickchart {
    background-color: var(--bg-color);
    border: none;
    border-radius: 50%;
    cursor: pointer;
    display: none;
    fill: var(--fill-color);
    height: 32px;
    padding: 0;
    width: 32px;
  }

  .colab-df-quickchart:hover {
    background-color: var(--hover-bg-color);
    box-shadow: 0 1px 2px rgba(60, 64, 67, 0.3), 0 1px 3px 1px rgba(60, 64, 67, 0.15);
    fill: var(--button-hover-fill-color);
  }

  .colab-df-quickchart-complete:disabled,
  .colab-df-quickchart-complete:disabled:hover {
    background-color: var(--disabled-bg-color);
    fill: var(--disabled-fill-color);
    box-shadow: none;
  }

  .colab-df-spinner {
    border: 2px solid var(--fill-color);
    border-color: transparent;
    border-bottom-color: var(--fill-color);
    animation:
      spin 1s steps(1) infinite;
  }

  @keyframes spin {
    0% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
      border-left-color: var(--fill-color);
    }
    20% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    30% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
      border-right-color: var(--fill-color);
    }
    40% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    60% {
      border-color: transparent;
      border-right-color: var(--fill-color);
    }
    80% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-bottom-color: var(--fill-color);
    }
    90% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
    }
  }
</style>

  <script>
    async function quickchart(key) {
      const quickchartButtonEl =
        document.querySelector('#' + key + ' button');
      quickchartButtonEl.disabled = true;  // To prevent multiple clicks.
      quickchartButtonEl.classList.add('colab-df-spinner');
      try {
        const charts = await google.colab.kernel.invokeFunction(
            'suggestCharts', [key], {});
      } catch (error) {
        console.error('Error during call to suggestCharts:', error);
      }
      quickchartButtonEl.classList.remove('colab-df-spinner');
      quickchartButtonEl.classList.add('colab-df-quickchart-complete');
    }
    (() => {
      let quickchartButtonEl =
        document.querySelector('#df-39b00ade-38df-4d9a-a399-a0958342bef9 button');
      quickchartButtonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';
    })();
  </script>
</div>

  <div id="id_0b243d96-7280-4223-a1a5-b4411a374e52">
    <style>
      .colab-df-generate {
        background-color: #E8F0FE;
        border: none;
        border-radius: 50%;
        cursor: pointer;
        display: none;
        fill: #1967D2;
        height: 32px;
        padding: 0 0 0 0;
        width: 32px;
      }

      .colab-df-generate:hover {
        background-color: #E2EBFA;
        box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
        fill: #174EA6;
      }

      [theme=dark] .colab-df-generate {
        background-color: #3B4455;
        fill: #D2E3FC;
      }

      [theme=dark] .colab-df-generate:hover {
        background-color: #434B5C;
        box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
        filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
        fill: #FFFFFF;
      }
    </style>
    <button class="colab-df-generate" onclick="generateWithVariable('population')"
            title="Generate code using this dataframe."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M7,19H8.4L18.45,9,17,7.55,7,17.6ZM5,21V16.75L18.45,3.32a2,2,0,0,1,2.83,0l1.4,1.43a1.91,1.91,0,0,1,.58,1.4,1.91,1.91,0,0,1-.58,1.4L9.25,21ZM18.45,9,17,7.55Zm-12,3A5.31,5.31,0,0,0,4.9,8.1,5.31,5.31,0,0,0,1,6.5,5.31,5.31,0,0,0,4.9,4.9,5.31,5.31,0,0,0,6.5,1,5.31,5.31,0,0,0,8.1,4.9,5.31,5.31,0,0,0,12,6.5,5.46,5.46,0,0,0,6.5,12Z"/>
  </svg>
    </button>
    <script>
      (() => {
      const buttonEl =
        document.querySelector('#id_0b243d96-7280-4223-a1a5-b4411a374e52 button.colab-df-generate');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      buttonEl.onclick = () => {
        google.colab.notebook.generateWithVariable('population');
      }
      })();
    </script>
  </div>

    </div>
  </div>





```python
import numpy as np
```


```python
s = pd.Series([1,3,5,np.nan,6,8])
```


```python
dates = pd.date_range('20130101',periods=6)
```


```python
df = pd.DataFrame(np.random.randn(6,4),index = dates,columns=["a","b","c","d"])
```


```python
df
```





  <div id="df-877b9c21-8a42-4177-ab2f-01ad992545ae" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>a</th>
      <th>b</th>
      <th>c</th>
      <th>d</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2013-01-01</th>
      <td>0.610012</td>
      <td>1.425700</td>
      <td>-0.322374</td>
      <td>2.711118</td>
    </tr>
    <tr>
      <th>2013-01-02</th>
      <td>-0.419979</td>
      <td>-0.461684</td>
      <td>-0.705384</td>
      <td>2.178186</td>
    </tr>
    <tr>
      <th>2013-01-03</th>
      <td>-1.992286</td>
      <td>1.268718</td>
      <td>1.448152</td>
      <td>-0.062125</td>
    </tr>
    <tr>
      <th>2013-01-04</th>
      <td>0.344872</td>
      <td>-0.928613</td>
      <td>-0.600000</td>
      <td>-0.962741</td>
    </tr>
    <tr>
      <th>2013-01-05</th>
      <td>0.021673</td>
      <td>-1.907069</td>
      <td>0.483322</td>
      <td>-0.593041</td>
    </tr>
    <tr>
      <th>2013-01-06</th>
      <td>1.337138</td>
      <td>-0.091313</td>
      <td>0.615604</td>
      <td>-1.030085</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-877b9c21-8a42-4177-ab2f-01ad992545ae')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-877b9c21-8a42-4177-ab2f-01ad992545ae button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-877b9c21-8a42-4177-ab2f-01ad992545ae');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


<div id="df-2bf6b266-0a69-46fe-8848-198979213e74">
  <button class="colab-df-quickchart" onclick="quickchart('df-2bf6b266-0a69-46fe-8848-198979213e74')"
            title="Suggest charts"
            style="display:none;">

<svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
     width="24px">
    <g>
        <path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zM9 17H7v-7h2v7zm4 0h-2V7h2v10zm4 0h-2v-4h2v4z"/>
    </g>
</svg>
  </button>

<style>
  .colab-df-quickchart {
      --bg-color: #E8F0FE;
      --fill-color: #1967D2;
      --hover-bg-color: #E2EBFA;
      --hover-fill-color: #174EA6;
      --disabled-fill-color: #AAA;
      --disabled-bg-color: #DDD;
  }

  [theme=dark] .colab-df-quickchart {
      --bg-color: #3B4455;
      --fill-color: #D2E3FC;
      --hover-bg-color: #434B5C;
      --hover-fill-color: #FFFFFF;
      --disabled-bg-color: #3B4455;
      --disabled-fill-color: #666;
  }

  .colab-df-quickchart {
    background-color: var(--bg-color);
    border: none;
    border-radius: 50%;
    cursor: pointer;
    display: none;
    fill: var(--fill-color);
    height: 32px;
    padding: 0;
    width: 32px;
  }

  .colab-df-quickchart:hover {
    background-color: var(--hover-bg-color);
    box-shadow: 0 1px 2px rgba(60, 64, 67, 0.3), 0 1px 3px 1px rgba(60, 64, 67, 0.15);
    fill: var(--button-hover-fill-color);
  }

  .colab-df-quickchart-complete:disabled,
  .colab-df-quickchart-complete:disabled:hover {
    background-color: var(--disabled-bg-color);
    fill: var(--disabled-fill-color);
    box-shadow: none;
  }

  .colab-df-spinner {
    border: 2px solid var(--fill-color);
    border-color: transparent;
    border-bottom-color: var(--fill-color);
    animation:
      spin 1s steps(1) infinite;
  }

  @keyframes spin {
    0% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
      border-left-color: var(--fill-color);
    }
    20% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    30% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
      border-right-color: var(--fill-color);
    }
    40% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    60% {
      border-color: transparent;
      border-right-color: var(--fill-color);
    }
    80% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-bottom-color: var(--fill-color);
    }
    90% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
    }
  }
</style>

  <script>
    async function quickchart(key) {
      const quickchartButtonEl =
        document.querySelector('#' + key + ' button');
      quickchartButtonEl.disabled = true;  // To prevent multiple clicks.
      quickchartButtonEl.classList.add('colab-df-spinner');
      try {
        const charts = await google.colab.kernel.invokeFunction(
            'suggestCharts', [key], {});
      } catch (error) {
        console.error('Error during call to suggestCharts:', error);
      }
      quickchartButtonEl.classList.remove('colab-df-spinner');
      quickchartButtonEl.classList.add('colab-df-quickchart-complete');
    }
    (() => {
      let quickchartButtonEl =
        document.querySelector('#df-2bf6b266-0a69-46fe-8848-198979213e74 button');
      quickchartButtonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';
    })();
  </script>
</div>

  <div id="id_1308c240-37cf-4495-9839-937f78b0b95e">
    <style>
      .colab-df-generate {
        background-color: #E8F0FE;
        border: none;
        border-radius: 50%;
        cursor: pointer;
        display: none;
        fill: #1967D2;
        height: 32px;
        padding: 0 0 0 0;
        width: 32px;
      }

      .colab-df-generate:hover {
        background-color: #E2EBFA;
        box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
        fill: #174EA6;
      }

      [theme=dark] .colab-df-generate {
        background-color: #3B4455;
        fill: #D2E3FC;
      }

      [theme=dark] .colab-df-generate:hover {
        background-color: #434B5C;
        box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
        filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
        fill: #FFFFFF;
      }
    </style>
    <button class="colab-df-generate" onclick="generateWithVariable('df')"
            title="Generate code using this dataframe."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M7,19H8.4L18.45,9,17,7.55,7,17.6ZM5,21V16.75L18.45,3.32a2,2,0,0,1,2.83,0l1.4,1.43a1.91,1.91,0,0,1,.58,1.4,1.91,1.91,0,0,1-.58,1.4L9.25,21ZM18.45,9,17,7.55Zm-12,3A5.31,5.31,0,0,0,4.9,8.1,5.31,5.31,0,0,0,1,6.5,5.31,5.31,0,0,0,4.9,4.9,5.31,5.31,0,0,0,6.5,1,5.31,5.31,0,0,0,8.1,4.9,5.31,5.31,0,0,0,12,6.5,5.46,5.46,0,0,0,6.5,12Z"/>
  </svg>
    </button>
    <script>
      (() => {
      const buttonEl =
        document.querySelector('#id_1308c240-37cf-4495-9839-937f78b0b95e button.colab-df-generate');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      buttonEl.onclick = () => {
        google.colab.notebook.generateWithVariable('df');
      }
      })();
    </script>
  </div>

    </div>
  </div>





```python
df.head(3)
```





  <div id="df-b314c6f8-d29b-4349-92d8-8a2ba7cff08e" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>a</th>
      <th>b</th>
      <th>c</th>
      <th>d</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2013-01-01</th>
      <td>0.610012</td>
      <td>1.425700</td>
      <td>-0.322374</td>
      <td>2.711118</td>
    </tr>
    <tr>
      <th>2013-01-02</th>
      <td>-0.419979</td>
      <td>-0.461684</td>
      <td>-0.705384</td>
      <td>2.178186</td>
    </tr>
    <tr>
      <th>2013-01-03</th>
      <td>-1.992286</td>
      <td>1.268718</td>
      <td>1.448152</td>
      <td>-0.062125</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-b314c6f8-d29b-4349-92d8-8a2ba7cff08e')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-b314c6f8-d29b-4349-92d8-8a2ba7cff08e button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-b314c6f8-d29b-4349-92d8-8a2ba7cff08e');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


<div id="df-3c0bcd5e-74d7-47bc-8c94-83621e3974d9">
  <button class="colab-df-quickchart" onclick="quickchart('df-3c0bcd5e-74d7-47bc-8c94-83621e3974d9')"
            title="Suggest charts"
            style="display:none;">

<svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
     width="24px">
    <g>
        <path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zM9 17H7v-7h2v7zm4 0h-2V7h2v10zm4 0h-2v-4h2v4z"/>
    </g>
</svg>
  </button>

<style>
  .colab-df-quickchart {
      --bg-color: #E8F0FE;
      --fill-color: #1967D2;
      --hover-bg-color: #E2EBFA;
      --hover-fill-color: #174EA6;
      --disabled-fill-color: #AAA;
      --disabled-bg-color: #DDD;
  }

  [theme=dark] .colab-df-quickchart {
      --bg-color: #3B4455;
      --fill-color: #D2E3FC;
      --hover-bg-color: #434B5C;
      --hover-fill-color: #FFFFFF;
      --disabled-bg-color: #3B4455;
      --disabled-fill-color: #666;
  }

  .colab-df-quickchart {
    background-color: var(--bg-color);
    border: none;
    border-radius: 50%;
    cursor: pointer;
    display: none;
    fill: var(--fill-color);
    height: 32px;
    padding: 0;
    width: 32px;
  }

  .colab-df-quickchart:hover {
    background-color: var(--hover-bg-color);
    box-shadow: 0 1px 2px rgba(60, 64, 67, 0.3), 0 1px 3px 1px rgba(60, 64, 67, 0.15);
    fill: var(--button-hover-fill-color);
  }

  .colab-df-quickchart-complete:disabled,
  .colab-df-quickchart-complete:disabled:hover {
    background-color: var(--disabled-bg-color);
    fill: var(--disabled-fill-color);
    box-shadow: none;
  }

  .colab-df-spinner {
    border: 2px solid var(--fill-color);
    border-color: transparent;
    border-bottom-color: var(--fill-color);
    animation:
      spin 1s steps(1) infinite;
  }

  @keyframes spin {
    0% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
      border-left-color: var(--fill-color);
    }
    20% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    30% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
      border-right-color: var(--fill-color);
    }
    40% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    60% {
      border-color: transparent;
      border-right-color: var(--fill-color);
    }
    80% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-bottom-color: var(--fill-color);
    }
    90% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
    }
  }
</style>

  <script>
    async function quickchart(key) {
      const quickchartButtonEl =
        document.querySelector('#' + key + ' button');
      quickchartButtonEl.disabled = true;  // To prevent multiple clicks.
      quickchartButtonEl.classList.add('colab-df-spinner');
      try {
        const charts = await google.colab.kernel.invokeFunction(
            'suggestCharts', [key], {});
      } catch (error) {
        console.error('Error during call to suggestCharts:', error);
      }
      quickchartButtonEl.classList.remove('colab-df-spinner');
      quickchartButtonEl.classList.add('colab-df-quickchart-complete');
    }
    (() => {
      let quickchartButtonEl =
        document.querySelector('#df-3c0bcd5e-74d7-47bc-8c94-83621e3974d9 button');
      quickchartButtonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';
    })();
  </script>
</div>

    </div>
  </div>





```python
df.tail(2)
```





  <div id="df-11b91e08-f767-4b6c-b8ac-7cb9a80ce071" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>a</th>
      <th>b</th>
      <th>c</th>
      <th>d</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2013-01-05</th>
      <td>0.021673</td>
      <td>-1.907069</td>
      <td>0.483322</td>
      <td>-0.593041</td>
    </tr>
    <tr>
      <th>2013-01-06</th>
      <td>1.337138</td>
      <td>-0.091313</td>
      <td>0.615604</td>
      <td>-1.030085</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-11b91e08-f767-4b6c-b8ac-7cb9a80ce071')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-11b91e08-f767-4b6c-b8ac-7cb9a80ce071 button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-11b91e08-f767-4b6c-b8ac-7cb9a80ce071');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


<div id="df-5be2f785-da71-4094-8f65-35939aaf7a5e">
  <button class="colab-df-quickchart" onclick="quickchart('df-5be2f785-da71-4094-8f65-35939aaf7a5e')"
            title="Suggest charts"
            style="display:none;">

<svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
     width="24px">
    <g>
        <path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zM9 17H7v-7h2v7zm4 0h-2V7h2v10zm4 0h-2v-4h2v4z"/>
    </g>
</svg>
  </button>

<style>
  .colab-df-quickchart {
      --bg-color: #E8F0FE;
      --fill-color: #1967D2;
      --hover-bg-color: #E2EBFA;
      --hover-fill-color: #174EA6;
      --disabled-fill-color: #AAA;
      --disabled-bg-color: #DDD;
  }

  [theme=dark] .colab-df-quickchart {
      --bg-color: #3B4455;
      --fill-color: #D2E3FC;
      --hover-bg-color: #434B5C;
      --hover-fill-color: #FFFFFF;
      --disabled-bg-color: #3B4455;
      --disabled-fill-color: #666;
  }

  .colab-df-quickchart {
    background-color: var(--bg-color);
    border: none;
    border-radius: 50%;
    cursor: pointer;
    display: none;
    fill: var(--fill-color);
    height: 32px;
    padding: 0;
    width: 32px;
  }

  .colab-df-quickchart:hover {
    background-color: var(--hover-bg-color);
    box-shadow: 0 1px 2px rgba(60, 64, 67, 0.3), 0 1px 3px 1px rgba(60, 64, 67, 0.15);
    fill: var(--button-hover-fill-color);
  }

  .colab-df-quickchart-complete:disabled,
  .colab-df-quickchart-complete:disabled:hover {
    background-color: var(--disabled-bg-color);
    fill: var(--disabled-fill-color);
    box-shadow: none;
  }

  .colab-df-spinner {
    border: 2px solid var(--fill-color);
    border-color: transparent;
    border-bottom-color: var(--fill-color);
    animation:
      spin 1s steps(1) infinite;
  }

  @keyframes spin {
    0% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
      border-left-color: var(--fill-color);
    }
    20% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    30% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
      border-right-color: var(--fill-color);
    }
    40% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    60% {
      border-color: transparent;
      border-right-color: var(--fill-color);
    }
    80% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-bottom-color: var(--fill-color);
    }
    90% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
    }
  }
</style>

  <script>
    async function quickchart(key) {
      const quickchartButtonEl =
        document.querySelector('#' + key + ' button');
      quickchartButtonEl.disabled = true;  // To prevent multiple clicks.
      quickchartButtonEl.classList.add('colab-df-spinner');
      try {
        const charts = await google.colab.kernel.invokeFunction(
            'suggestCharts', [key], {});
      } catch (error) {
        console.error('Error during call to suggestCharts:', error);
      }
      quickchartButtonEl.classList.remove('colab-df-spinner');
      quickchartButtonEl.classList.add('colab-df-quickchart-complete');
    }
    (() => {
      let quickchartButtonEl =
        document.querySelector('#df-5be2f785-da71-4094-8f65-35939aaf7a5e button');
      quickchartButtonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';
    })();
  </script>
</div>

    </div>
  </div>





```python
df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    DatetimeIndex: 6 entries, 2013-01-01 to 2013-01-06
    Freq: D
    Data columns (total 4 columns):
     #   Column  Non-Null Count  Dtype  
    ---  ------  --------------  -----  
     0   a       6 non-null      float64
     1   b       6 non-null      float64
     2   c       6 non-null      float64
     3   d       6 non-null      float64
    dtypes: float64(4)
    memory usage: 240.0 bytes



```python
df.describe()
```





  <div id="df-2c996ccd-c399-4853-8625-ace86123d39e" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>a</th>
      <th>b</th>
      <th>c</th>
      <th>d</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>6.000000</td>
      <td>6.000000</td>
      <td>6.000000</td>
      <td>6.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>-0.016428</td>
      <td>-0.115710</td>
      <td>0.153220</td>
      <td>0.373552</td>
    </tr>
    <tr>
      <th>std</th>
      <td>1.133440</td>
      <td>1.287063</td>
      <td>0.840279</td>
      <td>1.649298</td>
    </tr>
    <tr>
      <th>min</th>
      <td>-1.992286</td>
      <td>-1.907069</td>
      <td>-0.705384</td>
      <td>-1.030085</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>-0.309566</td>
      <td>-0.811880</td>
      <td>-0.530594</td>
      <td>-0.870316</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>0.183273</td>
      <td>-0.276498</td>
      <td>0.080474</td>
      <td>-0.327583</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>0.543727</td>
      <td>0.928710</td>
      <td>0.582533</td>
      <td>1.618108</td>
    </tr>
    <tr>
      <th>max</th>
      <td>1.337138</td>
      <td>1.425700</td>
      <td>1.448152</td>
      <td>2.711118</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-2c996ccd-c399-4853-8625-ace86123d39e')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-2c996ccd-c399-4853-8625-ace86123d39e button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-2c996ccd-c399-4853-8625-ace86123d39e');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


<div id="df-c8d775bd-3879-401c-81ae-8212427ba51b">
  <button class="colab-df-quickchart" onclick="quickchart('df-c8d775bd-3879-401c-81ae-8212427ba51b')"
            title="Suggest charts"
            style="display:none;">

<svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
     width="24px">
    <g>
        <path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zM9 17H7v-7h2v7zm4 0h-2V7h2v10zm4 0h-2v-4h2v4z"/>
    </g>
</svg>
  </button>

<style>
  .colab-df-quickchart {
      --bg-color: #E8F0FE;
      --fill-color: #1967D2;
      --hover-bg-color: #E2EBFA;
      --hover-fill-color: #174EA6;
      --disabled-fill-color: #AAA;
      --disabled-bg-color: #DDD;
  }

  [theme=dark] .colab-df-quickchart {
      --bg-color: #3B4455;
      --fill-color: #D2E3FC;
      --hover-bg-color: #434B5C;
      --hover-fill-color: #FFFFFF;
      --disabled-bg-color: #3B4455;
      --disabled-fill-color: #666;
  }

  .colab-df-quickchart {
    background-color: var(--bg-color);
    border: none;
    border-radius: 50%;
    cursor: pointer;
    display: none;
    fill: var(--fill-color);
    height: 32px;
    padding: 0;
    width: 32px;
  }

  .colab-df-quickchart:hover {
    background-color: var(--hover-bg-color);
    box-shadow: 0 1px 2px rgba(60, 64, 67, 0.3), 0 1px 3px 1px rgba(60, 64, 67, 0.15);
    fill: var(--button-hover-fill-color);
  }

  .colab-df-quickchart-complete:disabled,
  .colab-df-quickchart-complete:disabled:hover {
    background-color: var(--disabled-bg-color);
    fill: var(--disabled-fill-color);
    box-shadow: none;
  }

  .colab-df-spinner {
    border: 2px solid var(--fill-color);
    border-color: transparent;
    border-bottom-color: var(--fill-color);
    animation:
      spin 1s steps(1) infinite;
  }

  @keyframes spin {
    0% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
      border-left-color: var(--fill-color);
    }
    20% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    30% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
      border-right-color: var(--fill-color);
    }
    40% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    60% {
      border-color: transparent;
      border-right-color: var(--fill-color);
    }
    80% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-bottom-color: var(--fill-color);
    }
    90% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
    }
  }
</style>

  <script>
    async function quickchart(key) {
      const quickchartButtonEl =
        document.querySelector('#' + key + ' button');
      quickchartButtonEl.disabled = true;  // To prevent multiple clicks.
      quickchartButtonEl.classList.add('colab-df-spinner');
      try {
        const charts = await google.colab.kernel.invokeFunction(
            'suggestCharts', [key], {});
      } catch (error) {
        console.error('Error during call to suggestCharts:', error);
      }
      quickchartButtonEl.classList.remove('colab-df-spinner');
      quickchartButtonEl.classList.add('colab-df-quickchart-complete');
    }
    (() => {
      let quickchartButtonEl =
        document.querySelector('#df-c8d775bd-3879-401c-81ae-8212427ba51b button');
      quickchartButtonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';
    })();
  </script>
</div>

    </div>
  </div>





```python
df.sort_values(by = "b",ascending = False)
```





  <div id="df-abdb6f0b-beea-4eab-a0a5-93676aba08e5" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>a</th>
      <th>b</th>
      <th>c</th>
      <th>d</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2013-01-01</th>
      <td>0.610012</td>
      <td>1.425700</td>
      <td>-0.322374</td>
      <td>2.711118</td>
    </tr>
    <tr>
      <th>2013-01-03</th>
      <td>-1.992286</td>
      <td>1.268718</td>
      <td>1.448152</td>
      <td>-0.062125</td>
    </tr>
    <tr>
      <th>2013-01-06</th>
      <td>1.337138</td>
      <td>-0.091313</td>
      <td>0.615604</td>
      <td>-1.030085</td>
    </tr>
    <tr>
      <th>2013-01-02</th>
      <td>-0.419979</td>
      <td>-0.461684</td>
      <td>-0.705384</td>
      <td>2.178186</td>
    </tr>
    <tr>
      <th>2013-01-04</th>
      <td>0.344872</td>
      <td>-0.928613</td>
      <td>-0.600000</td>
      <td>-0.962741</td>
    </tr>
    <tr>
      <th>2013-01-05</th>
      <td>0.021673</td>
      <td>-1.907069</td>
      <td>0.483322</td>
      <td>-0.593041</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-abdb6f0b-beea-4eab-a0a5-93676aba08e5')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-abdb6f0b-beea-4eab-a0a5-93676aba08e5 button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-abdb6f0b-beea-4eab-a0a5-93676aba08e5');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


<div id="df-f3e6c87b-ba9b-4edc-bd36-bfd5145a53a7">
  <button class="colab-df-quickchart" onclick="quickchart('df-f3e6c87b-ba9b-4edc-bd36-bfd5145a53a7')"
            title="Suggest charts"
            style="display:none;">

<svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
     width="24px">
    <g>
        <path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zM9 17H7v-7h2v7zm4 0h-2V7h2v10zm4 0h-2v-4h2v4z"/>
    </g>
</svg>
  </button>

<style>
  .colab-df-quickchart {
      --bg-color: #E8F0FE;
      --fill-color: #1967D2;
      --hover-bg-color: #E2EBFA;
      --hover-fill-color: #174EA6;
      --disabled-fill-color: #AAA;
      --disabled-bg-color: #DDD;
  }

  [theme=dark] .colab-df-quickchart {
      --bg-color: #3B4455;
      --fill-color: #D2E3FC;
      --hover-bg-color: #434B5C;
      --hover-fill-color: #FFFFFF;
      --disabled-bg-color: #3B4455;
      --disabled-fill-color: #666;
  }

  .colab-df-quickchart {
    background-color: var(--bg-color);
    border: none;
    border-radius: 50%;
    cursor: pointer;
    display: none;
    fill: var(--fill-color);
    height: 32px;
    padding: 0;
    width: 32px;
  }

  .colab-df-quickchart:hover {
    background-color: var(--hover-bg-color);
    box-shadow: 0 1px 2px rgba(60, 64, 67, 0.3), 0 1px 3px 1px rgba(60, 64, 67, 0.15);
    fill: var(--button-hover-fill-color);
  }

  .colab-df-quickchart-complete:disabled,
  .colab-df-quickchart-complete:disabled:hover {
    background-color: var(--disabled-bg-color);
    fill: var(--disabled-fill-color);
    box-shadow: none;
  }

  .colab-df-spinner {
    border: 2px solid var(--fill-color);
    border-color: transparent;
    border-bottom-color: var(--fill-color);
    animation:
      spin 1s steps(1) infinite;
  }

  @keyframes spin {
    0% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
      border-left-color: var(--fill-color);
    }
    20% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    30% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
      border-right-color: var(--fill-color);
    }
    40% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    60% {
      border-color: transparent;
      border-right-color: var(--fill-color);
    }
    80% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-bottom-color: var(--fill-color);
    }
    90% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
    }
  }
</style>

  <script>
    async function quickchart(key) {
      const quickchartButtonEl =
        document.querySelector('#' + key + ' button');
      quickchartButtonEl.disabled = true;  // To prevent multiple clicks.
      quickchartButtonEl.classList.add('colab-df-spinner');
      try {
        const charts = await google.colab.kernel.invokeFunction(
            'suggestCharts', [key], {});
      } catch (error) {
        console.error('Error during call to suggestCharts:', error);
      }
      quickchartButtonEl.classList.remove('colab-df-spinner');
      quickchartButtonEl.classList.add('colab-df-quickchart-complete');
    }
    (() => {
      let quickchartButtonEl =
        document.querySelector('#df-f3e6c87b-ba9b-4edc-bd36-bfd5145a53a7 button');
      quickchartButtonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';
    })();
  </script>
</div>

    </div>
  </div>





```python
df[0:3] # 행을 자를때
```





  <div id="df-ad82a743-dda9-4fd6-863c-f95afc0171e2" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>a</th>
      <th>b</th>
      <th>c</th>
      <th>d</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2013-01-01</th>
      <td>0.610012</td>
      <td>1.425700</td>
      <td>-0.322374</td>
      <td>2.711118</td>
    </tr>
    <tr>
      <th>2013-01-02</th>
      <td>-0.419979</td>
      <td>-0.461684</td>
      <td>-0.705384</td>
      <td>2.178186</td>
    </tr>
    <tr>
      <th>2013-01-03</th>
      <td>-1.992286</td>
      <td>1.268718</td>
      <td>1.448152</td>
      <td>-0.062125</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-ad82a743-dda9-4fd6-863c-f95afc0171e2')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-ad82a743-dda9-4fd6-863c-f95afc0171e2 button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-ad82a743-dda9-4fd6-863c-f95afc0171e2');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


<div id="df-c11d24f0-d1c0-48a7-be0c-7492cb561666">
  <button class="colab-df-quickchart" onclick="quickchart('df-c11d24f0-d1c0-48a7-be0c-7492cb561666')"
            title="Suggest charts"
            style="display:none;">

<svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
     width="24px">
    <g>
        <path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zM9 17H7v-7h2v7zm4 0h-2V7h2v10zm4 0h-2v-4h2v4z"/>
    </g>
</svg>
  </button>

<style>
  .colab-df-quickchart {
      --bg-color: #E8F0FE;
      --fill-color: #1967D2;
      --hover-bg-color: #E2EBFA;
      --hover-fill-color: #174EA6;
      --disabled-fill-color: #AAA;
      --disabled-bg-color: #DDD;
  }

  [theme=dark] .colab-df-quickchart {
      --bg-color: #3B4455;
      --fill-color: #D2E3FC;
      --hover-bg-color: #434B5C;
      --hover-fill-color: #FFFFFF;
      --disabled-bg-color: #3B4455;
      --disabled-fill-color: #666;
  }

  .colab-df-quickchart {
    background-color: var(--bg-color);
    border: none;
    border-radius: 50%;
    cursor: pointer;
    display: none;
    fill: var(--fill-color);
    height: 32px;
    padding: 0;
    width: 32px;
  }

  .colab-df-quickchart:hover {
    background-color: var(--hover-bg-color);
    box-shadow: 0 1px 2px rgba(60, 64, 67, 0.3), 0 1px 3px 1px rgba(60, 64, 67, 0.15);
    fill: var(--button-hover-fill-color);
  }

  .colab-df-quickchart-complete:disabled,
  .colab-df-quickchart-complete:disabled:hover {
    background-color: var(--disabled-bg-color);
    fill: var(--disabled-fill-color);
    box-shadow: none;
  }

  .colab-df-spinner {
    border: 2px solid var(--fill-color);
    border-color: transparent;
    border-bottom-color: var(--fill-color);
    animation:
      spin 1s steps(1) infinite;
  }

  @keyframes spin {
    0% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
      border-left-color: var(--fill-color);
    }
    20% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    30% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
      border-right-color: var(--fill-color);
    }
    40% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    60% {
      border-color: transparent;
      border-right-color: var(--fill-color);
    }
    80% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-bottom-color: var(--fill-color);
    }
    90% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
    }
  }
</style>

  <script>
    async function quickchart(key) {
      const quickchartButtonEl =
        document.querySelector('#' + key + ' button');
      quickchartButtonEl.disabled = true;  // To prevent multiple clicks.
      quickchartButtonEl.classList.add('colab-df-spinner');
      try {
        const charts = await google.colab.kernel.invokeFunction(
            'suggestCharts', [key], {});
      } catch (error) {
        console.error('Error during call to suggestCharts:', error);
      }
      quickchartButtonEl.classList.remove('colab-df-spinner');
      quickchartButtonEl.classList.add('colab-df-quickchart-complete');
    }
    (() => {
      let quickchartButtonEl =
        document.querySelector('#df-c11d24f0-d1c0-48a7-be0c-7492cb561666 button');
      quickchartButtonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';
    })();
  </script>
</div>

    </div>
  </div>





```python
df['2013-01-02':'2013-01-03']
```





  <div id="df-82377b4e-32df-4c37-a9e8-55444a693824" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>a</th>
      <th>b</th>
      <th>c</th>
      <th>d</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2013-01-02</th>
      <td>-0.419979</td>
      <td>-0.461684</td>
      <td>-0.705384</td>
      <td>2.178186</td>
    </tr>
    <tr>
      <th>2013-01-03</th>
      <td>-1.992286</td>
      <td>1.268718</td>
      <td>1.448152</td>
      <td>-0.062125</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-82377b4e-32df-4c37-a9e8-55444a693824')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-82377b4e-32df-4c37-a9e8-55444a693824 button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-82377b4e-32df-4c37-a9e8-55444a693824');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


<div id="df-f03bbdf7-4102-4aff-8d6a-a273c8466811">
  <button class="colab-df-quickchart" onclick="quickchart('df-f03bbdf7-4102-4aff-8d6a-a273c8466811')"
            title="Suggest charts"
            style="display:none;">

<svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
     width="24px">
    <g>
        <path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zM9 17H7v-7h2v7zm4 0h-2V7h2v10zm4 0h-2v-4h2v4z"/>
    </g>
</svg>
  </button>

<style>
  .colab-df-quickchart {
      --bg-color: #E8F0FE;
      --fill-color: #1967D2;
      --hover-bg-color: #E2EBFA;
      --hover-fill-color: #174EA6;
      --disabled-fill-color: #AAA;
      --disabled-bg-color: #DDD;
  }

  [theme=dark] .colab-df-quickchart {
      --bg-color: #3B4455;
      --fill-color: #D2E3FC;
      --hover-bg-color: #434B5C;
      --hover-fill-color: #FFFFFF;
      --disabled-bg-color: #3B4455;
      --disabled-fill-color: #666;
  }

  .colab-df-quickchart {
    background-color: var(--bg-color);
    border: none;
    border-radius: 50%;
    cursor: pointer;
    display: none;
    fill: var(--fill-color);
    height: 32px;
    padding: 0;
    width: 32px;
  }

  .colab-df-quickchart:hover {
    background-color: var(--hover-bg-color);
    box-shadow: 0 1px 2px rgba(60, 64, 67, 0.3), 0 1px 3px 1px rgba(60, 64, 67, 0.15);
    fill: var(--button-hover-fill-color);
  }

  .colab-df-quickchart-complete:disabled,
  .colab-df-quickchart-complete:disabled:hover {
    background-color: var(--disabled-bg-color);
    fill: var(--disabled-fill-color);
    box-shadow: none;
  }

  .colab-df-spinner {
    border: 2px solid var(--fill-color);
    border-color: transparent;
    border-bottom-color: var(--fill-color);
    animation:
      spin 1s steps(1) infinite;
  }

  @keyframes spin {
    0% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
      border-left-color: var(--fill-color);
    }
    20% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    30% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
      border-right-color: var(--fill-color);
    }
    40% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    60% {
      border-color: transparent;
      border-right-color: var(--fill-color);
    }
    80% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-bottom-color: var(--fill-color);
    }
    90% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
    }
  }
</style>

  <script>
    async function quickchart(key) {
      const quickchartButtonEl =
        document.querySelector('#' + key + ' button');
      quickchartButtonEl.disabled = true;  // To prevent multiple clicks.
      quickchartButtonEl.classList.add('colab-df-spinner');
      try {
        const charts = await google.colab.kernel.invokeFunction(
            'suggestCharts', [key], {});
      } catch (error) {
        console.error('Error during call to suggestCharts:', error);
      }
      quickchartButtonEl.classList.remove('colab-df-spinner');
      quickchartButtonEl.classList.add('colab-df-quickchart-complete');
    }
    (() => {
      let quickchartButtonEl =
        document.querySelector('#df-f03bbdf7-4102-4aff-8d6a-a273c8466811 button');
      quickchartButtonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';
    })();
  </script>
</div>

    </div>
  </div>





```python
df.loc[:,'b':'c']
```





  <div id="df-d0be2432-1170-4f28-9bfe-349678ae2654" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>b</th>
      <th>c</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2013-01-01</th>
      <td>1.425700</td>
      <td>-0.322374</td>
    </tr>
    <tr>
      <th>2013-01-02</th>
      <td>-0.461684</td>
      <td>-0.705384</td>
    </tr>
    <tr>
      <th>2013-01-03</th>
      <td>1.268718</td>
      <td>1.448152</td>
    </tr>
    <tr>
      <th>2013-01-04</th>
      <td>-0.928613</td>
      <td>-0.600000</td>
    </tr>
    <tr>
      <th>2013-01-05</th>
      <td>-1.907069</td>
      <td>0.483322</td>
    </tr>
    <tr>
      <th>2013-01-06</th>
      <td>-0.091313</td>
      <td>0.615604</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-d0be2432-1170-4f28-9bfe-349678ae2654')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-d0be2432-1170-4f28-9bfe-349678ae2654 button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-d0be2432-1170-4f28-9bfe-349678ae2654');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


<div id="df-9815010f-c31f-41cd-90fe-9b2da2b3554f">
  <button class="colab-df-quickchart" onclick="quickchart('df-9815010f-c31f-41cd-90fe-9b2da2b3554f')"
            title="Suggest charts"
            style="display:none;">

<svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
     width="24px">
    <g>
        <path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zM9 17H7v-7h2v7zm4 0h-2V7h2v10zm4 0h-2v-4h2v4z"/>
    </g>
</svg>
  </button>

<style>
  .colab-df-quickchart {
      --bg-color: #E8F0FE;
      --fill-color: #1967D2;
      --hover-bg-color: #E2EBFA;
      --hover-fill-color: #174EA6;
      --disabled-fill-color: #AAA;
      --disabled-bg-color: #DDD;
  }

  [theme=dark] .colab-df-quickchart {
      --bg-color: #3B4455;
      --fill-color: #D2E3FC;
      --hover-bg-color: #434B5C;
      --hover-fill-color: #FFFFFF;
      --disabled-bg-color: #3B4455;
      --disabled-fill-color: #666;
  }

  .colab-df-quickchart {
    background-color: var(--bg-color);
    border: none;
    border-radius: 50%;
    cursor: pointer;
    display: none;
    fill: var(--fill-color);
    height: 32px;
    padding: 0;
    width: 32px;
  }

  .colab-df-quickchart:hover {
    background-color: var(--hover-bg-color);
    box-shadow: 0 1px 2px rgba(60, 64, 67, 0.3), 0 1px 3px 1px rgba(60, 64, 67, 0.15);
    fill: var(--button-hover-fill-color);
  }

  .colab-df-quickchart-complete:disabled,
  .colab-df-quickchart-complete:disabled:hover {
    background-color: var(--disabled-bg-color);
    fill: var(--disabled-fill-color);
    box-shadow: none;
  }

  .colab-df-spinner {
    border: 2px solid var(--fill-color);
    border-color: transparent;
    border-bottom-color: var(--fill-color);
    animation:
      spin 1s steps(1) infinite;
  }

  @keyframes spin {
    0% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
      border-left-color: var(--fill-color);
    }
    20% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    30% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
      border-right-color: var(--fill-color);
    }
    40% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    60% {
      border-color: transparent;
      border-right-color: var(--fill-color);
    }
    80% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-bottom-color: var(--fill-color);
    }
    90% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
    }
  }
</style>

  <script>
    async function quickchart(key) {
      const quickchartButtonEl =
        document.querySelector('#' + key + ' button');
      quickchartButtonEl.disabled = true;  // To prevent multiple clicks.
      quickchartButtonEl.classList.add('colab-df-spinner');
      try {
        const charts = await google.colab.kernel.invokeFunction(
            'suggestCharts', [key], {});
      } catch (error) {
        console.error('Error during call to suggestCharts:', error);
      }
      quickchartButtonEl.classList.remove('colab-df-spinner');
      quickchartButtonEl.classList.add('colab-df-quickchart-complete');
    }
    (() => {
      let quickchartButtonEl =
        document.querySelector('#df-9815010f-c31f-41cd-90fe-9b2da2b3554f button');
      quickchartButtonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';
    })();
  </script>
</div>

    </div>
  </div>





```python
df.loc[['2013-01-03','2013-01-05'],:]
```





  <div id="df-e8dea4a4-61e2-4e0d-b04b-4451e9f5eb8f" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>a</th>
      <th>b</th>
      <th>c</th>
      <th>d</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2013-01-03</th>
      <td>-1.992286</td>
      <td>1.268718</td>
      <td>1.448152</td>
      <td>-0.062125</td>
    </tr>
    <tr>
      <th>2013-01-05</th>
      <td>0.021673</td>
      <td>-1.907069</td>
      <td>0.483322</td>
      <td>-0.593041</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-e8dea4a4-61e2-4e0d-b04b-4451e9f5eb8f')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-e8dea4a4-61e2-4e0d-b04b-4451e9f5eb8f button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-e8dea4a4-61e2-4e0d-b04b-4451e9f5eb8f');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


<div id="df-ea87a43b-199b-4ffe-8be1-21e197164cb7">
  <button class="colab-df-quickchart" onclick="quickchart('df-ea87a43b-199b-4ffe-8be1-21e197164cb7')"
            title="Suggest charts"
            style="display:none;">

<svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
     width="24px">
    <g>
        <path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zM9 17H7v-7h2v7zm4 0h-2V7h2v10zm4 0h-2v-4h2v4z"/>
    </g>
</svg>
  </button>

<style>
  .colab-df-quickchart {
      --bg-color: #E8F0FE;
      --fill-color: #1967D2;
      --hover-bg-color: #E2EBFA;
      --hover-fill-color: #174EA6;
      --disabled-fill-color: #AAA;
      --disabled-bg-color: #DDD;
  }

  [theme=dark] .colab-df-quickchart {
      --bg-color: #3B4455;
      --fill-color: #D2E3FC;
      --hover-bg-color: #434B5C;
      --hover-fill-color: #FFFFFF;
      --disabled-bg-color: #3B4455;
      --disabled-fill-color: #666;
  }

  .colab-df-quickchart {
    background-color: var(--bg-color);
    border: none;
    border-radius: 50%;
    cursor: pointer;
    display: none;
    fill: var(--fill-color);
    height: 32px;
    padding: 0;
    width: 32px;
  }

  .colab-df-quickchart:hover {
    background-color: var(--hover-bg-color);
    box-shadow: 0 1px 2px rgba(60, 64, 67, 0.3), 0 1px 3px 1px rgba(60, 64, 67, 0.15);
    fill: var(--button-hover-fill-color);
  }

  .colab-df-quickchart-complete:disabled,
  .colab-df-quickchart-complete:disabled:hover {
    background-color: var(--disabled-bg-color);
    fill: var(--disabled-fill-color);
    box-shadow: none;
  }

  .colab-df-spinner {
    border: 2px solid var(--fill-color);
    border-color: transparent;
    border-bottom-color: var(--fill-color);
    animation:
      spin 1s steps(1) infinite;
  }

  @keyframes spin {
    0% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
      border-left-color: var(--fill-color);
    }
    20% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    30% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
      border-right-color: var(--fill-color);
    }
    40% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    60% {
      border-color: transparent;
      border-right-color: var(--fill-color);
    }
    80% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-bottom-color: var(--fill-color);
    }
    90% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
    }
  }
</style>

  <script>
    async function quickchart(key) {
      const quickchartButtonEl =
        document.querySelector('#' + key + ' button');
      quickchartButtonEl.disabled = true;  // To prevent multiple clicks.
      quickchartButtonEl.classList.add('colab-df-spinner');
      try {
        const charts = await google.colab.kernel.invokeFunction(
            'suggestCharts', [key], {});
      } catch (error) {
        console.error('Error during call to suggestCharts:', error);
      }
      quickchartButtonEl.classList.remove('colab-df-spinner');
      quickchartButtonEl.classList.add('colab-df-quickchart-complete');
    }
    (() => {
      let quickchartButtonEl =
        document.querySelector('#df-ea87a43b-199b-4ffe-8be1-21e197164cb7 button');
      quickchartButtonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';
    })();
  </script>
</div>

    </div>
  </div>





```python
df.iloc[3]
```




    a    0.344872
    b   -0.928613
    c   -0.600000
    d   -0.962741
    Name: 2013-01-04 00:00:00, dtype: float64




```python
df.iloc[2,1]
```





  <div id="df-e2c651a1-3683-45ae-ba3d-f4b234331ddc" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>b</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2013-01-03</th>
      <td>1.268718</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-e2c651a1-3683-45ae-ba3d-f4b234331ddc')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-e2c651a1-3683-45ae-ba3d-f4b234331ddc button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-e2c651a1-3683-45ae-ba3d-f4b234331ddc');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


    </div>
  </div>





```python
df.iloc[2,1]
```




    1.2687176617499407




```python
df
```





  <div id="df-01df5991-bb81-40d1-875c-6357be90a99b" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>a</th>
      <th>b</th>
      <th>c</th>
      <th>d</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2013-01-01</th>
      <td>0.610012</td>
      <td>1.425700</td>
      <td>-0.322374</td>
      <td>2.711118</td>
    </tr>
    <tr>
      <th>2013-01-02</th>
      <td>-0.419979</td>
      <td>-0.461684</td>
      <td>-0.705384</td>
      <td>2.178186</td>
    </tr>
    <tr>
      <th>2013-01-03</th>
      <td>-1.992286</td>
      <td>1.268718</td>
      <td>1.448152</td>
      <td>-0.062125</td>
    </tr>
    <tr>
      <th>2013-01-04</th>
      <td>0.344872</td>
      <td>-0.928613</td>
      <td>-0.600000</td>
      <td>-0.962741</td>
    </tr>
    <tr>
      <th>2013-01-05</th>
      <td>0.021673</td>
      <td>-1.907069</td>
      <td>0.483322</td>
      <td>-0.593041</td>
    </tr>
    <tr>
      <th>2013-01-06</th>
      <td>1.337138</td>
      <td>-0.091313</td>
      <td>0.615604</td>
      <td>-1.030085</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-01df5991-bb81-40d1-875c-6357be90a99b')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-01df5991-bb81-40d1-875c-6357be90a99b button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-01df5991-bb81-40d1-875c-6357be90a99b');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


<div id="df-e79bb565-1cc1-48e8-80cb-be1d59bc7551">
  <button class="colab-df-quickchart" onclick="quickchart('df-e79bb565-1cc1-48e8-80cb-be1d59bc7551')"
            title="Suggest charts"
            style="display:none;">

<svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
     width="24px">
    <g>
        <path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zM9 17H7v-7h2v7zm4 0h-2V7h2v10zm4 0h-2v-4h2v4z"/>
    </g>
</svg>
  </button>

<style>
  .colab-df-quickchart {
      --bg-color: #E8F0FE;
      --fill-color: #1967D2;
      --hover-bg-color: #E2EBFA;
      --hover-fill-color: #174EA6;
      --disabled-fill-color: #AAA;
      --disabled-bg-color: #DDD;
  }

  [theme=dark] .colab-df-quickchart {
      --bg-color: #3B4455;
      --fill-color: #D2E3FC;
      --hover-bg-color: #434B5C;
      --hover-fill-color: #FFFFFF;
      --disabled-bg-color: #3B4455;
      --disabled-fill-color: #666;
  }

  .colab-df-quickchart {
    background-color: var(--bg-color);
    border: none;
    border-radius: 50%;
    cursor: pointer;
    display: none;
    fill: var(--fill-color);
    height: 32px;
    padding: 0;
    width: 32px;
  }

  .colab-df-quickchart:hover {
    background-color: var(--hover-bg-color);
    box-shadow: 0 1px 2px rgba(60, 64, 67, 0.3), 0 1px 3px 1px rgba(60, 64, 67, 0.15);
    fill: var(--button-hover-fill-color);
  }

  .colab-df-quickchart-complete:disabled,
  .colab-df-quickchart-complete:disabled:hover {
    background-color: var(--disabled-bg-color);
    fill: var(--disabled-fill-color);
    box-shadow: none;
  }

  .colab-df-spinner {
    border: 2px solid var(--fill-color);
    border-color: transparent;
    border-bottom-color: var(--fill-color);
    animation:
      spin 1s steps(1) infinite;
  }

  @keyframes spin {
    0% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
      border-left-color: var(--fill-color);
    }
    20% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    30% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
      border-right-color: var(--fill-color);
    }
    40% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    60% {
      border-color: transparent;
      border-right-color: var(--fill-color);
    }
    80% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-bottom-color: var(--fill-color);
    }
    90% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
    }
  }
</style>

  <script>
    async function quickchart(key) {
      const quickchartButtonEl =
        document.querySelector('#' + key + ' button');
      quickchartButtonEl.disabled = true;  // To prevent multiple clicks.
      quickchartButtonEl.classList.add('colab-df-spinner');
      try {
        const charts = await google.colab.kernel.invokeFunction(
            'suggestCharts', [key], {});
      } catch (error) {
        console.error('Error during call to suggestCharts:', error);
      }
      quickchartButtonEl.classList.remove('colab-df-spinner');
      quickchartButtonEl.classList.add('colab-df-quickchart-complete');
    }
    (() => {
      let quickchartButtonEl =
        document.querySelector('#df-e79bb565-1cc1-48e8-80cb-be1d59bc7551 button');
      quickchartButtonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';
    })();
  </script>
</div>

  <div id="id_3eb5e652-832c-4b8b-9e11-0543180ebc56">
    <style>
      .colab-df-generate {
        background-color: #E8F0FE;
        border: none;
        border-radius: 50%;
        cursor: pointer;
        display: none;
        fill: #1967D2;
        height: 32px;
        padding: 0 0 0 0;
        width: 32px;
      }

      .colab-df-generate:hover {
        background-color: #E2EBFA;
        box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
        fill: #174EA6;
      }

      [theme=dark] .colab-df-generate {
        background-color: #3B4455;
        fill: #D2E3FC;
      }

      [theme=dark] .colab-df-generate:hover {
        background-color: #434B5C;
        box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
        filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
        fill: #FFFFFF;
      }
    </style>
    <button class="colab-df-generate" onclick="generateWithVariable('df')"
            title="Generate code using this dataframe."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M7,19H8.4L18.45,9,17,7.55,7,17.6ZM5,21V16.75L18.45,3.32a2,2,0,0,1,2.83,0l1.4,1.43a1.91,1.91,0,0,1,.58,1.4,1.91,1.91,0,0,1-.58,1.4L9.25,21ZM18.45,9,17,7.55Zm-12,3A5.31,5.31,0,0,0,4.9,8.1,5.31,5.31,0,0,0,1,6.5,5.31,5.31,0,0,0,4.9,4.9,5.31,5.31,0,0,0,6.5,1,5.31,5.31,0,0,0,8.1,4.9,5.31,5.31,0,0,0,12,6.5,5.46,5.46,0,0,0,6.5,12Z"/>
  </svg>
    </button>
    <script>
      (() => {
      const buttonEl =
        document.querySelector('#id_3eb5e652-832c-4b8b-9e11-0543180ebc56 button.colab-df-generate');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      buttonEl.onclick = () => {
        google.colab.notebook.generateWithVariable('df');
      }
      })();
    </script>
  </div>

    </div>
  </div>





```python
df['a']
```




    2013-01-01    0.610012
    2013-01-02   -0.419979
    2013-01-03   -1.992286
    2013-01-04    0.344872
    2013-01-05    0.021673
    2013-01-06    1.337138
    Freq: D, Name: a, dtype: float64




```python
df.a
```




    2013-01-01    0.610012
    2013-01-02   -0.419979
    2013-01-03   -1.992286
    2013-01-04    0.344872
    2013-01-05    0.021673
    2013-01-06    1.337138
    Freq: D, Name: a, dtype: float64




```python
df[df.a>0]
```





  <div id="df-511551b7-d3b7-420c-9d0a-58357fb89e43" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>a</th>
      <th>b</th>
      <th>c</th>
      <th>d</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2013-01-01</th>
      <td>0.610012</td>
      <td>1.425700</td>
      <td>-0.322374</td>
      <td>2.711118</td>
    </tr>
    <tr>
      <th>2013-01-04</th>
      <td>0.344872</td>
      <td>-0.928613</td>
      <td>-0.600000</td>
      <td>-0.962741</td>
    </tr>
    <tr>
      <th>2013-01-05</th>
      <td>0.021673</td>
      <td>-1.907069</td>
      <td>0.483322</td>
      <td>-0.593041</td>
    </tr>
    <tr>
      <th>2013-01-06</th>
      <td>1.337138</td>
      <td>-0.091313</td>
      <td>0.615604</td>
      <td>-1.030085</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-511551b7-d3b7-420c-9d0a-58357fb89e43')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-511551b7-d3b7-420c-9d0a-58357fb89e43 button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-511551b7-d3b7-420c-9d0a-58357fb89e43');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


<div id="df-04099e8d-300e-495b-a725-2a1697996d01">
  <button class="colab-df-quickchart" onclick="quickchart('df-04099e8d-300e-495b-a725-2a1697996d01')"
            title="Suggest charts"
            style="display:none;">

<svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
     width="24px">
    <g>
        <path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zM9 17H7v-7h2v7zm4 0h-2V7h2v10zm4 0h-2v-4h2v4z"/>
    </g>
</svg>
  </button>

<style>
  .colab-df-quickchart {
      --bg-color: #E8F0FE;
      --fill-color: #1967D2;
      --hover-bg-color: #E2EBFA;
      --hover-fill-color: #174EA6;
      --disabled-fill-color: #AAA;
      --disabled-bg-color: #DDD;
  }

  [theme=dark] .colab-df-quickchart {
      --bg-color: #3B4455;
      --fill-color: #D2E3FC;
      --hover-bg-color: #434B5C;
      --hover-fill-color: #FFFFFF;
      --disabled-bg-color: #3B4455;
      --disabled-fill-color: #666;
  }

  .colab-df-quickchart {
    background-color: var(--bg-color);
    border: none;
    border-radius: 50%;
    cursor: pointer;
    display: none;
    fill: var(--fill-color);
    height: 32px;
    padding: 0;
    width: 32px;
  }

  .colab-df-quickchart:hover {
    background-color: var(--hover-bg-color);
    box-shadow: 0 1px 2px rgba(60, 64, 67, 0.3), 0 1px 3px 1px rgba(60, 64, 67, 0.15);
    fill: var(--button-hover-fill-color);
  }

  .colab-df-quickchart-complete:disabled,
  .colab-df-quickchart-complete:disabled:hover {
    background-color: var(--disabled-bg-color);
    fill: var(--disabled-fill-color);
    box-shadow: none;
  }

  .colab-df-spinner {
    border: 2px solid var(--fill-color);
    border-color: transparent;
    border-bottom-color: var(--fill-color);
    animation:
      spin 1s steps(1) infinite;
  }

  @keyframes spin {
    0% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
      border-left-color: var(--fill-color);
    }
    20% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    30% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
      border-right-color: var(--fill-color);
    }
    40% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    60% {
      border-color: transparent;
      border-right-color: var(--fill-color);
    }
    80% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-bottom-color: var(--fill-color);
    }
    90% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
    }
  }
</style>

  <script>
    async function quickchart(key) {
      const quickchartButtonEl =
        document.querySelector('#' + key + ' button');
      quickchartButtonEl.disabled = true;  // To prevent multiple clicks.
      quickchartButtonEl.classList.add('colab-df-spinner');
      try {
        const charts = await google.colab.kernel.invokeFunction(
            'suggestCharts', [key], {});
      } catch (error) {
        console.error('Error during call to suggestCharts:', error);
      }
      quickchartButtonEl.classList.remove('colab-df-spinner');
      quickchartButtonEl.classList.add('colab-df-quickchart-complete');
    }
    (() => {
      let quickchartButtonEl =
        document.querySelector('#df-04099e8d-300e-495b-a725-2a1697996d01 button');
      quickchartButtonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';
    })();
  </script>
</div>

    </div>
  </div>





```python
df[df>0]
```





  <div id="df-60739312-9b82-4303-9961-72500114cb69" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>a</th>
      <th>b</th>
      <th>c</th>
      <th>d</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2013-01-01</th>
      <td>0.610012</td>
      <td>1.425700</td>
      <td>NaN</td>
      <td>2.711118</td>
    </tr>
    <tr>
      <th>2013-01-02</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2.178186</td>
    </tr>
    <tr>
      <th>2013-01-03</th>
      <td>NaN</td>
      <td>1.268718</td>
      <td>1.448152</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2013-01-04</th>
      <td>0.344872</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2013-01-05</th>
      <td>0.021673</td>
      <td>NaN</td>
      <td>0.483322</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2013-01-06</th>
      <td>1.337138</td>
      <td>NaN</td>
      <td>0.615604</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-60739312-9b82-4303-9961-72500114cb69')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-60739312-9b82-4303-9961-72500114cb69 button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-60739312-9b82-4303-9961-72500114cb69');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


<div id="df-e1db631c-ac06-4a73-8d8d-545fe74198e0">
  <button class="colab-df-quickchart" onclick="quickchart('df-e1db631c-ac06-4a73-8d8d-545fe74198e0')"
            title="Suggest charts"
            style="display:none;">

<svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
     width="24px">
    <g>
        <path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zM9 17H7v-7h2v7zm4 0h-2V7h2v10zm4 0h-2v-4h2v4z"/>
    </g>
</svg>
  </button>

<style>
  .colab-df-quickchart {
      --bg-color: #E8F0FE;
      --fill-color: #1967D2;
      --hover-bg-color: #E2EBFA;
      --hover-fill-color: #174EA6;
      --disabled-fill-color: #AAA;
      --disabled-bg-color: #DDD;
  }

  [theme=dark] .colab-df-quickchart {
      --bg-color: #3B4455;
      --fill-color: #D2E3FC;
      --hover-bg-color: #434B5C;
      --hover-fill-color: #FFFFFF;
      --disabled-bg-color: #3B4455;
      --disabled-fill-color: #666;
  }

  .colab-df-quickchart {
    background-color: var(--bg-color);
    border: none;
    border-radius: 50%;
    cursor: pointer;
    display: none;
    fill: var(--fill-color);
    height: 32px;
    padding: 0;
    width: 32px;
  }

  .colab-df-quickchart:hover {
    background-color: var(--hover-bg-color);
    box-shadow: 0 1px 2px rgba(60, 64, 67, 0.3), 0 1px 3px 1px rgba(60, 64, 67, 0.15);
    fill: var(--button-hover-fill-color);
  }

  .colab-df-quickchart-complete:disabled,
  .colab-df-quickchart-complete:disabled:hover {
    background-color: var(--disabled-bg-color);
    fill: var(--disabled-fill-color);
    box-shadow: none;
  }

  .colab-df-spinner {
    border: 2px solid var(--fill-color);
    border-color: transparent;
    border-bottom-color: var(--fill-color);
    animation:
      spin 1s steps(1) infinite;
  }

  @keyframes spin {
    0% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
      border-left-color: var(--fill-color);
    }
    20% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    30% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
      border-right-color: var(--fill-color);
    }
    40% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    60% {
      border-color: transparent;
      border-right-color: var(--fill-color);
    }
    80% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-bottom-color: var(--fill-color);
    }
    90% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
    }
  }
</style>

  <script>
    async function quickchart(key) {
      const quickchartButtonEl =
        document.querySelector('#' + key + ' button');
      quickchartButtonEl.disabled = true;  // To prevent multiple clicks.
      quickchartButtonEl.classList.add('colab-df-spinner');
      try {
        const charts = await google.colab.kernel.invokeFunction(
            'suggestCharts', [key], {});
      } catch (error) {
        console.error('Error during call to suggestCharts:', error);
      }
      quickchartButtonEl.classList.remove('colab-df-spinner');
      quickchartButtonEl.classList.add('colab-df-quickchart-complete');
    }
    (() => {
      let quickchartButtonEl =
        document.querySelector('#df-e1db631c-ac06-4a73-8d8d-545fe74198e0 button');
      quickchartButtonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';
    })();
  </script>
</div>

    </div>
  </div>





```python
df2 = df.copy()
```


```python
df2
```





  <div id="df-78615ee2-d3a4-40cd-93f9-093c6e09f6a0" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>a</th>
      <th>b</th>
      <th>c</th>
      <th>d</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2013-01-01</th>
      <td>0.610012</td>
      <td>1.425700</td>
      <td>-0.322374</td>
      <td>2.711118</td>
    </tr>
    <tr>
      <th>2013-01-02</th>
      <td>-0.419979</td>
      <td>-0.461684</td>
      <td>-0.705384</td>
      <td>2.178186</td>
    </tr>
    <tr>
      <th>2013-01-03</th>
      <td>-1.992286</td>
      <td>1.268718</td>
      <td>1.448152</td>
      <td>-0.062125</td>
    </tr>
    <tr>
      <th>2013-01-04</th>
      <td>0.344872</td>
      <td>-0.928613</td>
      <td>-0.600000</td>
      <td>-0.962741</td>
    </tr>
    <tr>
      <th>2013-01-05</th>
      <td>0.021673</td>
      <td>-1.907069</td>
      <td>0.483322</td>
      <td>-0.593041</td>
    </tr>
    <tr>
      <th>2013-01-06</th>
      <td>1.337138</td>
      <td>-0.091313</td>
      <td>0.615604</td>
      <td>-1.030085</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-78615ee2-d3a4-40cd-93f9-093c6e09f6a0')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-78615ee2-d3a4-40cd-93f9-093c6e09f6a0 button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-78615ee2-d3a4-40cd-93f9-093c6e09f6a0');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


<div id="df-fc9a3394-5a0f-453a-9df2-59536d263ad4">
  <button class="colab-df-quickchart" onclick="quickchart('df-fc9a3394-5a0f-453a-9df2-59536d263ad4')"
            title="Suggest charts"
            style="display:none;">

<svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
     width="24px">
    <g>
        <path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zM9 17H7v-7h2v7zm4 0h-2V7h2v10zm4 0h-2v-4h2v4z"/>
    </g>
</svg>
  </button>

<style>
  .colab-df-quickchart {
      --bg-color: #E8F0FE;
      --fill-color: #1967D2;
      --hover-bg-color: #E2EBFA;
      --hover-fill-color: #174EA6;
      --disabled-fill-color: #AAA;
      --disabled-bg-color: #DDD;
  }

  [theme=dark] .colab-df-quickchart {
      --bg-color: #3B4455;
      --fill-color: #D2E3FC;
      --hover-bg-color: #434B5C;
      --hover-fill-color: #FFFFFF;
      --disabled-bg-color: #3B4455;
      --disabled-fill-color: #666;
  }

  .colab-df-quickchart {
    background-color: var(--bg-color);
    border: none;
    border-radius: 50%;
    cursor: pointer;
    display: none;
    fill: var(--fill-color);
    height: 32px;
    padding: 0;
    width: 32px;
  }

  .colab-df-quickchart:hover {
    background-color: var(--hover-bg-color);
    box-shadow: 0 1px 2px rgba(60, 64, 67, 0.3), 0 1px 3px 1px rgba(60, 64, 67, 0.15);
    fill: var(--button-hover-fill-color);
  }

  .colab-df-quickchart-complete:disabled,
  .colab-df-quickchart-complete:disabled:hover {
    background-color: var(--disabled-bg-color);
    fill: var(--disabled-fill-color);
    box-shadow: none;
  }

  .colab-df-spinner {
    border: 2px solid var(--fill-color);
    border-color: transparent;
    border-bottom-color: var(--fill-color);
    animation:
      spin 1s steps(1) infinite;
  }

  @keyframes spin {
    0% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
      border-left-color: var(--fill-color);
    }
    20% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    30% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
      border-right-color: var(--fill-color);
    }
    40% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    60% {
      border-color: transparent;
      border-right-color: var(--fill-color);
    }
    80% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-bottom-color: var(--fill-color);
    }
    90% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
    }
  }
</style>

  <script>
    async function quickchart(key) {
      const quickchartButtonEl =
        document.querySelector('#' + key + ' button');
      quickchartButtonEl.disabled = true;  // To prevent multiple clicks.
      quickchartButtonEl.classList.add('colab-df-spinner');
      try {
        const charts = await google.colab.kernel.invokeFunction(
            'suggestCharts', [key], {});
      } catch (error) {
        console.error('Error during call to suggestCharts:', error);
      }
      quickchartButtonEl.classList.remove('colab-df-spinner');
      quickchartButtonEl.classList.add('colab-df-quickchart-complete');
    }
    (() => {
      let quickchartButtonEl =
        document.querySelector('#df-fc9a3394-5a0f-453a-9df2-59536d263ad4 button');
      quickchartButtonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';
    })();
  </script>
</div>

  <div id="id_5027d0eb-6523-4797-8274-245fa3574e81">
    <style>
      .colab-df-generate {
        background-color: #E8F0FE;
        border: none;
        border-radius: 50%;
        cursor: pointer;
        display: none;
        fill: #1967D2;
        height: 32px;
        padding: 0 0 0 0;
        width: 32px;
      }

      .colab-df-generate:hover {
        background-color: #E2EBFA;
        box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
        fill: #174EA6;
      }

      [theme=dark] .colab-df-generate {
        background-color: #3B4455;
        fill: #D2E3FC;
      }

      [theme=dark] .colab-df-generate:hover {
        background-color: #434B5C;
        box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
        filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
        fill: #FFFFFF;
      }
    </style>
    <button class="colab-df-generate" onclick="generateWithVariable('df2')"
            title="Generate code using this dataframe."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M7,19H8.4L18.45,9,17,7.55,7,17.6ZM5,21V16.75L18.45,3.32a2,2,0,0,1,2.83,0l1.4,1.43a1.91,1.91,0,0,1,.58,1.4,1.91,1.91,0,0,1-.58,1.4L9.25,21ZM18.45,9,17,7.55Zm-12,3A5.31,5.31,0,0,0,4.9,8.1,5.31,5.31,0,0,0,1,6.5,5.31,5.31,0,0,0,4.9,4.9,5.31,5.31,0,0,0,6.5,1,5.31,5.31,0,0,0,8.1,4.9,5.31,5.31,0,0,0,12,6.5,5.46,5.46,0,0,0,6.5,12Z"/>
  </svg>
    </button>
    <script>
      (() => {
      const buttonEl =
        document.querySelector('#id_5027d0eb-6523-4797-8274-245fa3574e81 button.colab-df-generate');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      buttonEl.onclick = () => {
        google.colab.notebook.generateWithVariable('df2');
      }
      })();
    </script>
  </div>

    </div>
  </div>





```python
df2['e'] = [11,22,33,44,55,66]
```


```python
df2
```





  <div id="df-521e43fb-e629-4e61-80eb-a932ac6b0167" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>a</th>
      <th>b</th>
      <th>c</th>
      <th>d</th>
      <th>e</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2013-01-01</th>
      <td>0.610012</td>
      <td>1.425700</td>
      <td>-0.322374</td>
      <td>2.711118</td>
      <td>11</td>
    </tr>
    <tr>
      <th>2013-01-02</th>
      <td>-0.419979</td>
      <td>-0.461684</td>
      <td>-0.705384</td>
      <td>2.178186</td>
      <td>22</td>
    </tr>
    <tr>
      <th>2013-01-03</th>
      <td>-1.992286</td>
      <td>1.268718</td>
      <td>1.448152</td>
      <td>-0.062125</td>
      <td>33</td>
    </tr>
    <tr>
      <th>2013-01-04</th>
      <td>0.344872</td>
      <td>-0.928613</td>
      <td>-0.600000</td>
      <td>-0.962741</td>
      <td>44</td>
    </tr>
    <tr>
      <th>2013-01-05</th>
      <td>0.021673</td>
      <td>-1.907069</td>
      <td>0.483322</td>
      <td>-0.593041</td>
      <td>55</td>
    </tr>
    <tr>
      <th>2013-01-06</th>
      <td>1.337138</td>
      <td>-0.091313</td>
      <td>0.615604</td>
      <td>-1.030085</td>
      <td>66</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-521e43fb-e629-4e61-80eb-a932ac6b0167')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-521e43fb-e629-4e61-80eb-a932ac6b0167 button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-521e43fb-e629-4e61-80eb-a932ac6b0167');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


<div id="df-af99d13e-dc06-4c96-89ff-3d87939db9c1">
  <button class="colab-df-quickchart" onclick="quickchart('df-af99d13e-dc06-4c96-89ff-3d87939db9c1')"
            title="Suggest charts"
            style="display:none;">

<svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
     width="24px">
    <g>
        <path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zM9 17H7v-7h2v7zm4 0h-2V7h2v10zm4 0h-2v-4h2v4z"/>
    </g>
</svg>
  </button>

<style>
  .colab-df-quickchart {
      --bg-color: #E8F0FE;
      --fill-color: #1967D2;
      --hover-bg-color: #E2EBFA;
      --hover-fill-color: #174EA6;
      --disabled-fill-color: #AAA;
      --disabled-bg-color: #DDD;
  }

  [theme=dark] .colab-df-quickchart {
      --bg-color: #3B4455;
      --fill-color: #D2E3FC;
      --hover-bg-color: #434B5C;
      --hover-fill-color: #FFFFFF;
      --disabled-bg-color: #3B4455;
      --disabled-fill-color: #666;
  }

  .colab-df-quickchart {
    background-color: var(--bg-color);
    border: none;
    border-radius: 50%;
    cursor: pointer;
    display: none;
    fill: var(--fill-color);
    height: 32px;
    padding: 0;
    width: 32px;
  }

  .colab-df-quickchart:hover {
    background-color: var(--hover-bg-color);
    box-shadow: 0 1px 2px rgba(60, 64, 67, 0.3), 0 1px 3px 1px rgba(60, 64, 67, 0.15);
    fill: var(--button-hover-fill-color);
  }

  .colab-df-quickchart-complete:disabled,
  .colab-df-quickchart-complete:disabled:hover {
    background-color: var(--disabled-bg-color);
    fill: var(--disabled-fill-color);
    box-shadow: none;
  }

  .colab-df-spinner {
    border: 2px solid var(--fill-color);
    border-color: transparent;
    border-bottom-color: var(--fill-color);
    animation:
      spin 1s steps(1) infinite;
  }

  @keyframes spin {
    0% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
      border-left-color: var(--fill-color);
    }
    20% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    30% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
      border-right-color: var(--fill-color);
    }
    40% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    60% {
      border-color: transparent;
      border-right-color: var(--fill-color);
    }
    80% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-bottom-color: var(--fill-color);
    }
    90% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
    }
  }
</style>

  <script>
    async function quickchart(key) {
      const quickchartButtonEl =
        document.querySelector('#' + key + ' button');
      quickchartButtonEl.disabled = true;  // To prevent multiple clicks.
      quickchartButtonEl.classList.add('colab-df-spinner');
      try {
        const charts = await google.colab.kernel.invokeFunction(
            'suggestCharts', [key], {});
      } catch (error) {
        console.error('Error during call to suggestCharts:', error);
      }
      quickchartButtonEl.classList.remove('colab-df-spinner');
      quickchartButtonEl.classList.add('colab-df-quickchart-complete');
    }
    (() => {
      let quickchartButtonEl =
        document.querySelector('#df-af99d13e-dc06-4c96-89ff-3d87939db9c1 button');
      quickchartButtonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';
    })();
  </script>
</div>

  <div id="id_bf6479ea-b66c-4019-89a5-a84b114015d3">
    <style>
      .colab-df-generate {
        background-color: #E8F0FE;
        border: none;
        border-radius: 50%;
        cursor: pointer;
        display: none;
        fill: #1967D2;
        height: 32px;
        padding: 0 0 0 0;
        width: 32px;
      }

      .colab-df-generate:hover {
        background-color: #E2EBFA;
        box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
        fill: #174EA6;
      }

      [theme=dark] .colab-df-generate {
        background-color: #3B4455;
        fill: #D2E3FC;
      }

      [theme=dark] .colab-df-generate:hover {
        background-color: #434B5C;
        box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
        filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
        fill: #FFFFFF;
      }
    </style>
    <button class="colab-df-generate" onclick="generateWithVariable('df2')"
            title="Generate code using this dataframe."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M7,19H8.4L18.45,9,17,7.55,7,17.6ZM5,21V16.75L18.45,3.32a2,2,0,0,1,2.83,0l1.4,1.43a1.91,1.91,0,0,1,.58,1.4,1.91,1.91,0,0,1-.58,1.4L9.25,21ZM18.45,9,17,7.55Zm-12,3A5.31,5.31,0,0,0,4.9,8.1,5.31,5.31,0,0,0,1,6.5,5.31,5.31,0,0,0,4.9,4.9,5.31,5.31,0,0,0,6.5,1,5.31,5.31,0,0,0,8.1,4.9,5.31,5.31,0,0,0,12,6.5,5.46,5.46,0,0,0,6.5,12Z"/>
  </svg>
    </button>
    <script>
      (() => {
      const buttonEl =
        document.querySelector('#id_bf6479ea-b66c-4019-89a5-a84b114015d3 button.colab-df-generate');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      buttonEl.onclick = () => {
        google.colab.notebook.generateWithVariable('df2');
      }
      })();
    </script>
  </div>

    </div>
  </div>





```python
df2['e'].isin([11,33])
```




    2013-01-01     True
    2013-01-02    False
    2013-01-03     True
    2013-01-04    False
    2013-01-05    False
    2013-01-06    False
    Freq: D, Name: e, dtype: bool




```python
df2[df2['e'].isin([44,55])]
```





  <div id="df-f522b86f-6916-42c7-b8b3-323d557e3834" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>a</th>
      <th>b</th>
      <th>c</th>
      <th>d</th>
      <th>e</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2013-01-04</th>
      <td>0.344872</td>
      <td>-0.928613</td>
      <td>-0.600000</td>
      <td>-0.962741</td>
      <td>44</td>
    </tr>
    <tr>
      <th>2013-01-05</th>
      <td>0.021673</td>
      <td>-1.907069</td>
      <td>0.483322</td>
      <td>-0.593041</td>
      <td>55</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-f522b86f-6916-42c7-b8b3-323d557e3834')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-f522b86f-6916-42c7-b8b3-323d557e3834 button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-f522b86f-6916-42c7-b8b3-323d557e3834');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


<div id="df-8486d886-0af3-4269-9e12-9f65e1e84d38">
  <button class="colab-df-quickchart" onclick="quickchart('df-8486d886-0af3-4269-9e12-9f65e1e84d38')"
            title="Suggest charts"
            style="display:none;">

<svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
     width="24px">
    <g>
        <path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zM9 17H7v-7h2v7zm4 0h-2V7h2v10zm4 0h-2v-4h2v4z"/>
    </g>
</svg>
  </button>

<style>
  .colab-df-quickchart {
      --bg-color: #E8F0FE;
      --fill-color: #1967D2;
      --hover-bg-color: #E2EBFA;
      --hover-fill-color: #174EA6;
      --disabled-fill-color: #AAA;
      --disabled-bg-color: #DDD;
  }

  [theme=dark] .colab-df-quickchart {
      --bg-color: #3B4455;
      --fill-color: #D2E3FC;
      --hover-bg-color: #434B5C;
      --hover-fill-color: #FFFFFF;
      --disabled-bg-color: #3B4455;
      --disabled-fill-color: #666;
  }

  .colab-df-quickchart {
    background-color: var(--bg-color);
    border: none;
    border-radius: 50%;
    cursor: pointer;
    display: none;
    fill: var(--fill-color);
    height: 32px;
    padding: 0;
    width: 32px;
  }

  .colab-df-quickchart:hover {
    background-color: var(--hover-bg-color);
    box-shadow: 0 1px 2px rgba(60, 64, 67, 0.3), 0 1px 3px 1px rgba(60, 64, 67, 0.15);
    fill: var(--button-hover-fill-color);
  }

  .colab-df-quickchart-complete:disabled,
  .colab-df-quickchart-complete:disabled:hover {
    background-color: var(--disabled-bg-color);
    fill: var(--disabled-fill-color);
    box-shadow: none;
  }

  .colab-df-spinner {
    border: 2px solid var(--fill-color);
    border-color: transparent;
    border-bottom-color: var(--fill-color);
    animation:
      spin 1s steps(1) infinite;
  }

  @keyframes spin {
    0% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
      border-left-color: var(--fill-color);
    }
    20% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    30% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
      border-right-color: var(--fill-color);
    }
    40% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    60% {
      border-color: transparent;
      border-right-color: var(--fill-color);
    }
    80% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-bottom-color: var(--fill-color);
    }
    90% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
    }
  }
</style>

  <script>
    async function quickchart(key) {
      const quickchartButtonEl =
        document.querySelector('#' + key + ' button');
      quickchartButtonEl.disabled = true;  // To prevent multiple clicks.
      quickchartButtonEl.classList.add('colab-df-spinner');
      try {
        const charts = await google.colab.kernel.invokeFunction(
            'suggestCharts', [key], {});
      } catch (error) {
        console.error('Error during call to suggestCharts:', error);
      }
      quickchartButtonEl.classList.remove('colab-df-spinner');
      quickchartButtonEl.classList.add('colab-df-quickchart-complete');
    }
    (() => {
      let quickchartButtonEl =
        document.querySelector('#df-8486d886-0af3-4269-9e12-9f65e1e84d38 button');
      quickchartButtonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';
    })();
  </script>
</div>

    </div>
  </div>





```python
df
```





  <div id="df-29acf3cb-035a-4b6a-a56d-e1b59114ae0c" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>a</th>
      <th>b</th>
      <th>c</th>
      <th>d</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2013-01-01</th>
      <td>0.610012</td>
      <td>1.425700</td>
      <td>-0.322374</td>
      <td>2.711118</td>
    </tr>
    <tr>
      <th>2013-01-02</th>
      <td>-0.419979</td>
      <td>-0.461684</td>
      <td>-0.705384</td>
      <td>2.178186</td>
    </tr>
    <tr>
      <th>2013-01-03</th>
      <td>-1.992286</td>
      <td>1.268718</td>
      <td>1.448152</td>
      <td>-0.062125</td>
    </tr>
    <tr>
      <th>2013-01-04</th>
      <td>0.344872</td>
      <td>-0.928613</td>
      <td>-0.600000</td>
      <td>-0.962741</td>
    </tr>
    <tr>
      <th>2013-01-05</th>
      <td>0.021673</td>
      <td>-1.907069</td>
      <td>0.483322</td>
      <td>-0.593041</td>
    </tr>
    <tr>
      <th>2013-01-06</th>
      <td>1.337138</td>
      <td>-0.091313</td>
      <td>0.615604</td>
      <td>-1.030085</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-29acf3cb-035a-4b6a-a56d-e1b59114ae0c')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-29acf3cb-035a-4b6a-a56d-e1b59114ae0c button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-29acf3cb-035a-4b6a-a56d-e1b59114ae0c');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


<div id="df-a2bae5fb-2551-4696-8a76-5851fb4c2b9b">
  <button class="colab-df-quickchart" onclick="quickchart('df-a2bae5fb-2551-4696-8a76-5851fb4c2b9b')"
            title="Suggest charts"
            style="display:none;">

<svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
     width="24px">
    <g>
        <path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zM9 17H7v-7h2v7zm4 0h-2V7h2v10zm4 0h-2v-4h2v4z"/>
    </g>
</svg>
  </button>

<style>
  .colab-df-quickchart {
      --bg-color: #E8F0FE;
      --fill-color: #1967D2;
      --hover-bg-color: #E2EBFA;
      --hover-fill-color: #174EA6;
      --disabled-fill-color: #AAA;
      --disabled-bg-color: #DDD;
  }

  [theme=dark] .colab-df-quickchart {
      --bg-color: #3B4455;
      --fill-color: #D2E3FC;
      --hover-bg-color: #434B5C;
      --hover-fill-color: #FFFFFF;
      --disabled-bg-color: #3B4455;
      --disabled-fill-color: #666;
  }

  .colab-df-quickchart {
    background-color: var(--bg-color);
    border: none;
    border-radius: 50%;
    cursor: pointer;
    display: none;
    fill: var(--fill-color);
    height: 32px;
    padding: 0;
    width: 32px;
  }

  .colab-df-quickchart:hover {
    background-color: var(--hover-bg-color);
    box-shadow: 0 1px 2px rgba(60, 64, 67, 0.3), 0 1px 3px 1px rgba(60, 64, 67, 0.15);
    fill: var(--button-hover-fill-color);
  }

  .colab-df-quickchart-complete:disabled,
  .colab-df-quickchart-complete:disabled:hover {
    background-color: var(--disabled-bg-color);
    fill: var(--disabled-fill-color);
    box-shadow: none;
  }

  .colab-df-spinner {
    border: 2px solid var(--fill-color);
    border-color: transparent;
    border-bottom-color: var(--fill-color);
    animation:
      spin 1s steps(1) infinite;
  }

  @keyframes spin {
    0% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
      border-left-color: var(--fill-color);
    }
    20% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    30% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
      border-right-color: var(--fill-color);
    }
    40% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    60% {
      border-color: transparent;
      border-right-color: var(--fill-color);
    }
    80% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-bottom-color: var(--fill-color);
    }
    90% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
    }
  }
</style>

  <script>
    async function quickchart(key) {
      const quickchartButtonEl =
        document.querySelector('#' + key + ' button');
      quickchartButtonEl.disabled = true;  // To prevent multiple clicks.
      quickchartButtonEl.classList.add('colab-df-spinner');
      try {
        const charts = await google.colab.kernel.invokeFunction(
            'suggestCharts', [key], {});
      } catch (error) {
        console.error('Error during call to suggestCharts:', error);
      }
      quickchartButtonEl.classList.remove('colab-df-spinner');
      quickchartButtonEl.classList.add('colab-df-quickchart-complete');
    }
    (() => {
      let quickchartButtonEl =
        document.querySelector('#df-a2bae5fb-2551-4696-8a76-5851fb4c2b9b button');
      quickchartButtonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';
    })();
  </script>
</div>

  <div id="id_b3b4c9d7-974a-45d8-b214-271d592ba564">
    <style>
      .colab-df-generate {
        background-color: #E8F0FE;
        border: none;
        border-radius: 50%;
        cursor: pointer;
        display: none;
        fill: #1967D2;
        height: 32px;
        padding: 0 0 0 0;
        width: 32px;
      }

      .colab-df-generate:hover {
        background-color: #E2EBFA;
        box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
        fill: #174EA6;
      }

      [theme=dark] .colab-df-generate {
        background-color: #3B4455;
        fill: #D2E3FC;
      }

      [theme=dark] .colab-df-generate:hover {
        background-color: #434B5C;
        box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
        filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
        fill: #FFFFFF;
      }
    </style>
    <button class="colab-df-generate" onclick="generateWithVariable('df')"
            title="Generate code using this dataframe."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M7,19H8.4L18.45,9,17,7.55,7,17.6ZM5,21V16.75L18.45,3.32a2,2,0,0,1,2.83,0l1.4,1.43a1.91,1.91,0,0,1,.58,1.4,1.91,1.91,0,0,1-.58,1.4L9.25,21ZM18.45,9,17,7.55Zm-12,3A5.31,5.31,0,0,0,4.9,8.1,5.31,5.31,0,0,0,1,6.5,5.31,5.31,0,0,0,4.9,4.9,5.31,5.31,0,0,0,6.5,1,5.31,5.31,0,0,0,8.1,4.9,5.31,5.31,0,0,0,12,6.5,5.46,5.46,0,0,0,6.5,12Z"/>
  </svg>
    </button>
    <script>
      (() => {
      const buttonEl =
        document.querySelector('#id_b3b4c9d7-974a-45d8-b214-271d592ba564 button.colab-df-generate');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      buttonEl.onclick = () => {
        google.colab.notebook.generateWithVariable('df');
      }
      })();
    </script>
  </div>

    </div>
  </div>





```python
df.apply(np.cumsum)
```





  <div id="df-9b88e915-c185-468e-b9e1-468ac12fc07a" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>a</th>
      <th>b</th>
      <th>c</th>
      <th>d</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2013-01-01</th>
      <td>0.610012</td>
      <td>1.425700</td>
      <td>-0.322374</td>
      <td>2.711118</td>
    </tr>
    <tr>
      <th>2013-01-02</th>
      <td>0.190033</td>
      <td>0.964016</td>
      <td>-1.027758</td>
      <td>4.889304</td>
    </tr>
    <tr>
      <th>2013-01-03</th>
      <td>-1.802253</td>
      <td>2.232734</td>
      <td>0.420394</td>
      <td>4.827179</td>
    </tr>
    <tr>
      <th>2013-01-04</th>
      <td>-1.457381</td>
      <td>1.304121</td>
      <td>-0.179606</td>
      <td>3.864437</td>
    </tr>
    <tr>
      <th>2013-01-05</th>
      <td>-1.435707</td>
      <td>-0.602948</td>
      <td>0.303716</td>
      <td>3.271397</td>
    </tr>
    <tr>
      <th>2013-01-06</th>
      <td>-0.098569</td>
      <td>-0.694260</td>
      <td>0.919320</td>
      <td>2.241312</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-9b88e915-c185-468e-b9e1-468ac12fc07a')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-9b88e915-c185-468e-b9e1-468ac12fc07a button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-9b88e915-c185-468e-b9e1-468ac12fc07a');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


<div id="df-39ae4eb6-6452-4eb4-95e1-5d8b4631c293">
  <button class="colab-df-quickchart" onclick="quickchart('df-39ae4eb6-6452-4eb4-95e1-5d8b4631c293')"
            title="Suggest charts"
            style="display:none;">

<svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
     width="24px">
    <g>
        <path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zM9 17H7v-7h2v7zm4 0h-2V7h2v10zm4 0h-2v-4h2v4z"/>
    </g>
</svg>
  </button>

<style>
  .colab-df-quickchart {
      --bg-color: #E8F0FE;
      --fill-color: #1967D2;
      --hover-bg-color: #E2EBFA;
      --hover-fill-color: #174EA6;
      --disabled-fill-color: #AAA;
      --disabled-bg-color: #DDD;
  }

  [theme=dark] .colab-df-quickchart {
      --bg-color: #3B4455;
      --fill-color: #D2E3FC;
      --hover-bg-color: #434B5C;
      --hover-fill-color: #FFFFFF;
      --disabled-bg-color: #3B4455;
      --disabled-fill-color: #666;
  }

  .colab-df-quickchart {
    background-color: var(--bg-color);
    border: none;
    border-radius: 50%;
    cursor: pointer;
    display: none;
    fill: var(--fill-color);
    height: 32px;
    padding: 0;
    width: 32px;
  }

  .colab-df-quickchart:hover {
    background-color: var(--hover-bg-color);
    box-shadow: 0 1px 2px rgba(60, 64, 67, 0.3), 0 1px 3px 1px rgba(60, 64, 67, 0.15);
    fill: var(--button-hover-fill-color);
  }

  .colab-df-quickchart-complete:disabled,
  .colab-df-quickchart-complete:disabled:hover {
    background-color: var(--disabled-bg-color);
    fill: var(--disabled-fill-color);
    box-shadow: none;
  }

  .colab-df-spinner {
    border: 2px solid var(--fill-color);
    border-color: transparent;
    border-bottom-color: var(--fill-color);
    animation:
      spin 1s steps(1) infinite;
  }

  @keyframes spin {
    0% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
      border-left-color: var(--fill-color);
    }
    20% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    30% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
      border-right-color: var(--fill-color);
    }
    40% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    60% {
      border-color: transparent;
      border-right-color: var(--fill-color);
    }
    80% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-bottom-color: var(--fill-color);
    }
    90% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
    }
  }
</style>

  <script>
    async function quickchart(key) {
      const quickchartButtonEl =
        document.querySelector('#' + key + ' button');
      quickchartButtonEl.disabled = true;  // To prevent multiple clicks.
      quickchartButtonEl.classList.add('colab-df-spinner');
      try {
        const charts = await google.colab.kernel.invokeFunction(
            'suggestCharts', [key], {});
      } catch (error) {
        console.error('Error during call to suggestCharts:', error);
      }
      quickchartButtonEl.classList.remove('colab-df-spinner');
      quickchartButtonEl.classList.add('colab-df-quickchart-complete');
    }
    (() => {
      let quickchartButtonEl =
        document.querySelector('#df-39ae4eb6-6452-4eb4-95e1-5d8b4631c293 button');
      quickchartButtonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';
    })();
  </script>
</div>

    </div>
  </div>





```python
df.apply(lambda x:x.max() - x.min())
```




    a    3.329424
    b    3.332769
    c    2.153536
    d    3.741203
    dtype: float64




```python
cctv_seoul.head()
```





  <div id="df-29b3a64f-a143-4608-8721-2f59565db805" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Unnamed: 0</th>
      <th>구분</th>
      <th>총계</th>
      <th>2014년 이전</th>
      <th>2014년</th>
      <th>2015년</th>
      <th>2016년</th>
      <th>2017년</th>
      <th>2018년</th>
      <th>2019년</th>
      <th>2020년</th>
      <th>2021년</th>
      <th>2022년</th>
      <th>2023년</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>NaN</td>
      <td>계</td>
      <td>92,991</td>
      <td>5,896</td>
      <td>3,927</td>
      <td>6,416</td>
      <td>8,812</td>
      <td>11,598</td>
      <td>10,887</td>
      <td>13,083</td>
      <td>11,803</td>
      <td>9,396</td>
      <td>9,302</td>
      <td>1,871</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1.0</td>
      <td>종로구</td>
      <td>1,966</td>
      <td>553</td>
      <td>103</td>
      <td>160</td>
      <td>130</td>
      <td>144</td>
      <td>150</td>
      <td>67</td>
      <td>233</td>
      <td>88</td>
      <td>207</td>
      <td>131</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2.0</td>
      <td>중 구</td>
      <td>2,584</td>
      <td>217</td>
      <td>77</td>
      <td>236</td>
      <td>240</td>
      <td>372</td>
      <td>386</td>
      <td>155</td>
      <td>361</td>
      <td>403</td>
      <td>137</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3.0</td>
      <td>용산구</td>
      <td>2,970</td>
      <td>87</td>
      <td>68</td>
      <td>83</td>
      <td>295</td>
      <td>491</td>
      <td>115</td>
      <td>322</td>
      <td>623</td>
      <td>422</td>
      <td>464</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4.0</td>
      <td>성동구</td>
      <td>4,175</td>
      <td>157</td>
      <td>110</td>
      <td>366</td>
      <td>281</td>
      <td>942</td>
      <td>457</td>
      <td>643</td>
      <td>485</td>
      <td>367</td>
      <td>232</td>
      <td>135</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-29b3a64f-a143-4608-8721-2f59565db805')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-29b3a64f-a143-4608-8721-2f59565db805 button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-29b3a64f-a143-4608-8721-2f59565db805');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


<div id="df-ad4b93a1-1000-4a16-a048-ae0f940156a2">
  <button class="colab-df-quickchart" onclick="quickchart('df-ad4b93a1-1000-4a16-a048-ae0f940156a2')"
            title="Suggest charts"
            style="display:none;">

<svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
     width="24px">
    <g>
        <path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zM9 17H7v-7h2v7zm4 0h-2V7h2v10zm4 0h-2v-4h2v4z"/>
    </g>
</svg>
  </button>

<style>
  .colab-df-quickchart {
      --bg-color: #E8F0FE;
      --fill-color: #1967D2;
      --hover-bg-color: #E2EBFA;
      --hover-fill-color: #174EA6;
      --disabled-fill-color: #AAA;
      --disabled-bg-color: #DDD;
  }

  [theme=dark] .colab-df-quickchart {
      --bg-color: #3B4455;
      --fill-color: #D2E3FC;
      --hover-bg-color: #434B5C;
      --hover-fill-color: #FFFFFF;
      --disabled-bg-color: #3B4455;
      --disabled-fill-color: #666;
  }

  .colab-df-quickchart {
    background-color: var(--bg-color);
    border: none;
    border-radius: 50%;
    cursor: pointer;
    display: none;
    fill: var(--fill-color);
    height: 32px;
    padding: 0;
    width: 32px;
  }

  .colab-df-quickchart:hover {
    background-color: var(--hover-bg-color);
    box-shadow: 0 1px 2px rgba(60, 64, 67, 0.3), 0 1px 3px 1px rgba(60, 64, 67, 0.15);
    fill: var(--button-hover-fill-color);
  }

  .colab-df-quickchart-complete:disabled,
  .colab-df-quickchart-complete:disabled:hover {
    background-color: var(--disabled-bg-color);
    fill: var(--disabled-fill-color);
    box-shadow: none;
  }

  .colab-df-spinner {
    border: 2px solid var(--fill-color);
    border-color: transparent;
    border-bottom-color: var(--fill-color);
    animation:
      spin 1s steps(1) infinite;
  }

  @keyframes spin {
    0% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
      border-left-color: var(--fill-color);
    }
    20% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    30% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
      border-right-color: var(--fill-color);
    }
    40% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    60% {
      border-color: transparent;
      border-right-color: var(--fill-color);
    }
    80% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-bottom-color: var(--fill-color);
    }
    90% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
    }
  }
</style>

  <script>
    async function quickchart(key) {
      const quickchartButtonEl =
        document.querySelector('#' + key + ' button');
      quickchartButtonEl.disabled = true;  // To prevent multiple clicks.
      quickchartButtonEl.classList.add('colab-df-spinner');
      try {
        const charts = await google.colab.kernel.invokeFunction(
            'suggestCharts', [key], {});
      } catch (error) {
        console.error('Error during call to suggestCharts:', error);
      }
      quickchartButtonEl.classList.remove('colab-df-spinner');
      quickchartButtonEl.classList.add('colab-df-quickchart-complete');
    }
    (() => {
      let quickchartButtonEl =
        document.querySelector('#df-ad4b93a1-1000-4a16-a048-ae0f940156a2 button');
      quickchartButtonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';
    })();
  </script>
</div>

    </div>
  </div>





```python
cctv_seoul.sort_values(by="총계", ascending = False)
```





  <div id="df-cbe255d4-a6d6-4f36-9845-4827c5bf14e8" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Unnamed: 0</th>
      <th>구분</th>
      <th>총계</th>
      <th>2014년 이전</th>
      <th>2014년</th>
      <th>2015년</th>
      <th>2016년</th>
      <th>2017년</th>
      <th>2018년</th>
      <th>2019년</th>
      <th>2020년</th>
      <th>2021년</th>
      <th>2022년</th>
      <th>2023년</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>NaN</td>
      <td>계</td>
      <td>92,991</td>
      <td>5,896</td>
      <td>3,927</td>
      <td>6,416</td>
      <td>8,812</td>
      <td>11,598</td>
      <td>10,887</td>
      <td>13,083</td>
      <td>11,803</td>
      <td>9,396</td>
      <td>9,302</td>
      <td>1,871</td>
    </tr>
    <tr>
      <th>23</th>
      <td>23.0</td>
      <td>강남구</td>
      <td>7,243</td>
      <td>121</td>
      <td>573</td>
      <td>826</td>
      <td>1295</td>
      <td>989</td>
      <td>744</td>
      <td>790</td>
      <td>923</td>
      <td>514</td>
      <td>468</td>
      <td>0</td>
    </tr>
    <tr>
      <th>21</th>
      <td>21.0</td>
      <td>관악구</td>
      <td>5,642</td>
      <td>592</td>
      <td>277</td>
      <td>448</td>
      <td>607</td>
      <td>675</td>
      <td>663</td>
      <td>889</td>
      <td>404</td>
      <td>549</td>
      <td>135</td>
      <td>403</td>
    </tr>
    <tr>
      <th>22</th>
      <td>22.0</td>
      <td>서초구</td>
      <td>4,995</td>
      <td>160</td>
      <td>70</td>
      <td>559</td>
      <td>518</td>
      <td>1054</td>
      <td>426</td>
      <td>344</td>
      <td>422</td>
      <td>566</td>
      <td>876</td>
      <td>0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>8.0</td>
      <td>성북구</td>
      <td>4,957</td>
      <td>318</td>
      <td>229</td>
      <td>322</td>
      <td>594</td>
      <td>811</td>
      <td>867</td>
      <td>714</td>
      <td>253</td>
      <td>407</td>
      <td>338</td>
      <td>104</td>
    </tr>
    <tr>
      <th>17</th>
      <td>17.0</td>
      <td>구로구</td>
      <td>4,831</td>
      <td>307</td>
      <td>160</td>
      <td>263</td>
      <td>351</td>
      <td>559</td>
      <td>542</td>
      <td>789</td>
      <td>652</td>
      <td>689</td>
      <td>341</td>
      <td>178</td>
    </tr>
    <tr>
      <th>19</th>
      <td>19.0</td>
      <td>영등포구</td>
      <td>4,660</td>
      <td>190</td>
      <td>295</td>
      <td>360</td>
      <td>285</td>
      <td>422</td>
      <td>803</td>
      <td>153</td>
      <td>1357</td>
      <td>329</td>
      <td>450</td>
      <td>16</td>
    </tr>
    <tr>
      <th>12</th>
      <td>12.0</td>
      <td>은평구</td>
      <td>4,653</td>
      <td>61</td>
      <td>332</td>
      <td>329</td>
      <td>555</td>
      <td>403</td>
      <td>635</td>
      <td>1,057</td>
      <td>288</td>
      <td>471</td>
      <td>522</td>
      <td>0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>7.0</td>
      <td>중랑구</td>
      <td>4,193</td>
      <td>555</td>
      <td>104</td>
      <td>145</td>
      <td>153</td>
      <td>170</td>
      <td>215</td>
      <td>1074</td>
      <td>976</td>
      <td>507</td>
      <td>294</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4.0</td>
      <td>성동구</td>
      <td>4,175</td>
      <td>157</td>
      <td>110</td>
      <td>366</td>
      <td>281</td>
      <td>942</td>
      <td>457</td>
      <td>643</td>
      <td>485</td>
      <td>367</td>
      <td>232</td>
      <td>135</td>
    </tr>
    <tr>
      <th>15</th>
      <td>15.0</td>
      <td>양천구</td>
      <td>3,890</td>
      <td>369</td>
      <td>129</td>
      <td>243</td>
      <td>423</td>
      <td>564</td>
      <td>833</td>
      <td>311</td>
      <td>352</td>
      <td>374</td>
      <td>292</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>5</th>
      <td>5.0</td>
      <td>광진구</td>
      <td>3,592</td>
      <td>130</td>
      <td>176</td>
      <td>95</td>
      <td>40</td>
      <td>666</td>
      <td>460</td>
      <td>712</td>
      <td>190</td>
      <td>738</td>
      <td>273</td>
      <td>112</td>
    </tr>
    <tr>
      <th>13</th>
      <td>13.0</td>
      <td>서대문구</td>
      <td>3,538</td>
      <td>1,012</td>
      <td>114</td>
      <td>109</td>
      <td>277</td>
      <td>416</td>
      <td>246</td>
      <td>325</td>
      <td>441</td>
      <td>331</td>
      <td>174</td>
      <td>93</td>
    </tr>
    <tr>
      <th>16</th>
      <td>16.0</td>
      <td>강서구</td>
      <td>3,353</td>
      <td>132</td>
      <td>200</td>
      <td>192</td>
      <td>167</td>
      <td>507</td>
      <td>259</td>
      <td>455</td>
      <td>363</td>
      <td>441</td>
      <td>596</td>
      <td>41</td>
    </tr>
    <tr>
      <th>9</th>
      <td>9.0</td>
      <td>강북구</td>
      <td>3,321</td>
      <td>21</td>
      <td>16</td>
      <td>68</td>
      <td>210</td>
      <td>4</td>
      <td>375</td>
      <td>963</td>
      <td>569</td>
      <td>298</td>
      <td>797</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>24</th>
      <td>24.0</td>
      <td>송파구</td>
      <td>3,253</td>
      <td>149</td>
      <td>74</td>
      <td>183</td>
      <td>126</td>
      <td>227</td>
      <td>526</td>
      <td>1050</td>
      <td>237</td>
      <td>151</td>
      <td>431</td>
      <td>99</td>
    </tr>
    <tr>
      <th>25</th>
      <td>25.0</td>
      <td>강동구</td>
      <td>3,192</td>
      <td>277</td>
      <td>56</td>
      <td>174</td>
      <td>226</td>
      <td>351</td>
      <td>379</td>
      <td>362</td>
      <td>635</td>
      <td>301</td>
      <td>429</td>
      <td>2</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3.0</td>
      <td>용산구</td>
      <td>2,970</td>
      <td>87</td>
      <td>68</td>
      <td>83</td>
      <td>295</td>
      <td>491</td>
      <td>115</td>
      <td>322</td>
      <td>623</td>
      <td>422</td>
      <td>464</td>
      <td>0</td>
    </tr>
    <tr>
      <th>18</th>
      <td>18.0</td>
      <td>금천구</td>
      <td>2,885</td>
      <td>NaN</td>
      <td>43</td>
      <td>350</td>
      <td>132</td>
      <td>196</td>
      <td>539</td>
      <td>366</td>
      <td>513</td>
      <td>207</td>
      <td>304</td>
      <td>235</td>
    </tr>
    <tr>
      <th>6</th>
      <td>6.0</td>
      <td>동대문구</td>
      <td>2,759</td>
      <td>3</td>
      <td>5</td>
      <td>72</td>
      <td>774</td>
      <td>702</td>
      <td>201</td>
      <td>218</td>
      <td>229</td>
      <td>229</td>
      <td>317</td>
      <td>9</td>
    </tr>
    <tr>
      <th>20</th>
      <td>20.0</td>
      <td>동작구</td>
      <td>2,690</td>
      <td>88</td>
      <td>478</td>
      <td>127</td>
      <td>255</td>
      <td>270</td>
      <td>299</td>
      <td>311</td>
      <td>433</td>
      <td>30</td>
      <td>373</td>
      <td>26</td>
    </tr>
    <tr>
      <th>14</th>
      <td>14.0</td>
      <td>마포구</td>
      <td>2,638</td>
      <td>61</td>
      <td>109</td>
      <td>170</td>
      <td>458</td>
      <td>376</td>
      <td>368</td>
      <td>494</td>
      <td>298</td>
      <td>177</td>
      <td>117</td>
      <td>10</td>
    </tr>
    <tr>
      <th>11</th>
      <td>11.0</td>
      <td>노원구</td>
      <td>2,626</td>
      <td>238</td>
      <td>75</td>
      <td>509</td>
      <td>329</td>
      <td>171</td>
      <td>216</td>
      <td>324</td>
      <td>387</td>
      <td>215</td>
      <td>144</td>
      <td>18</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2.0</td>
      <td>중 구</td>
      <td>2,584</td>
      <td>217</td>
      <td>77</td>
      <td>236</td>
      <td>240</td>
      <td>372</td>
      <td>386</td>
      <td>155</td>
      <td>361</td>
      <td>403</td>
      <td>137</td>
      <td>0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>10.0</td>
      <td>도봉구</td>
      <td>2,385</td>
      <td>98</td>
      <td>54</td>
      <td>27</td>
      <td>91</td>
      <td>116</td>
      <td>183</td>
      <td>195</td>
      <td>179</td>
      <td>592</td>
      <td>591</td>
      <td>259</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1.0</td>
      <td>종로구</td>
      <td>1,966</td>
      <td>553</td>
      <td>103</td>
      <td>160</td>
      <td>130</td>
      <td>144</td>
      <td>150</td>
      <td>67</td>
      <td>233</td>
      <td>88</td>
      <td>207</td>
      <td>131</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-cbe255d4-a6d6-4f36-9845-4827c5bf14e8')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-cbe255d4-a6d6-4f36-9845-4827c5bf14e8 button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-cbe255d4-a6d6-4f36-9845-4827c5bf14e8');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


<div id="df-a01558bd-0f99-4ba0-98b1-71aa3a738ad9">
  <button class="colab-df-quickchart" onclick="quickchart('df-a01558bd-0f99-4ba0-98b1-71aa3a738ad9')"
            title="Suggest charts"
            style="display:none;">

<svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
     width="24px">
    <g>
        <path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zM9 17H7v-7h2v7zm4 0h-2V7h2v10zm4 0h-2v-4h2v4z"/>
    </g>
</svg>
  </button>

<style>
  .colab-df-quickchart {
      --bg-color: #E8F0FE;
      --fill-color: #1967D2;
      --hover-bg-color: #E2EBFA;
      --hover-fill-color: #174EA6;
      --disabled-fill-color: #AAA;
      --disabled-bg-color: #DDD;
  }

  [theme=dark] .colab-df-quickchart {
      --bg-color: #3B4455;
      --fill-color: #D2E3FC;
      --hover-bg-color: #434B5C;
      --hover-fill-color: #FFFFFF;
      --disabled-bg-color: #3B4455;
      --disabled-fill-color: #666;
  }

  .colab-df-quickchart {
    background-color: var(--bg-color);
    border: none;
    border-radius: 50%;
    cursor: pointer;
    display: none;
    fill: var(--fill-color);
    height: 32px;
    padding: 0;
    width: 32px;
  }

  .colab-df-quickchart:hover {
    background-color: var(--hover-bg-color);
    box-shadow: 0 1px 2px rgba(60, 64, 67, 0.3), 0 1px 3px 1px rgba(60, 64, 67, 0.15);
    fill: var(--button-hover-fill-color);
  }

  .colab-df-quickchart-complete:disabled,
  .colab-df-quickchart-complete:disabled:hover {
    background-color: var(--disabled-bg-color);
    fill: var(--disabled-fill-color);
    box-shadow: none;
  }

  .colab-df-spinner {
    border: 2px solid var(--fill-color);
    border-color: transparent;
    border-bottom-color: var(--fill-color);
    animation:
      spin 1s steps(1) infinite;
  }

  @keyframes spin {
    0% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
      border-left-color: var(--fill-color);
    }
    20% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    30% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
      border-right-color: var(--fill-color);
    }
    40% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    60% {
      border-color: transparent;
      border-right-color: var(--fill-color);
    }
    80% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-bottom-color: var(--fill-color);
    }
    90% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
    }
  }
</style>

  <script>
    async function quickchart(key) {
      const quickchartButtonEl =
        document.querySelector('#' + key + ' button');
      quickchartButtonEl.disabled = true;  // To prevent multiple clicks.
      quickchartButtonEl.classList.add('colab-df-spinner');
      try {
        const charts = await google.colab.kernel.invokeFunction(
            'suggestCharts', [key], {});
      } catch (error) {
        console.error('Error during call to suggestCharts:', error);
      }
      quickchartButtonEl.classList.remove('colab-df-spinner');
      quickchartButtonEl.classList.add('colab-df-quickchart-complete');
    }
    (() => {
      let quickchartButtonEl =
        document.querySelector('#df-a01558bd-0f99-4ba0-98b1-71aa3a738ad9 button');
      quickchartButtonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';
    })();
  </script>
</div>

    </div>
  </div>





```python
cctv_seoul.sort_values(by="2022년",ascending = False)
```





  <div id="df-30dbbafd-5863-44a7-ae86-6fe5319e0c4e" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Unnamed: 0</th>
      <th>구분</th>
      <th>총계</th>
      <th>2014년 이전</th>
      <th>2014년</th>
      <th>2015년</th>
      <th>2016년</th>
      <th>2017년</th>
      <th>2018년</th>
      <th>2019년</th>
      <th>2020년</th>
      <th>2021년</th>
      <th>2022년</th>
      <th>2023년</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>NaN</td>
      <td>계</td>
      <td>92,991</td>
      <td>5,896</td>
      <td>3,927</td>
      <td>6,416</td>
      <td>8,812</td>
      <td>11,598</td>
      <td>10,887</td>
      <td>13,083</td>
      <td>11,803</td>
      <td>9,396</td>
      <td>9,302</td>
      <td>1,871</td>
    </tr>
    <tr>
      <th>22</th>
      <td>22.0</td>
      <td>서초구</td>
      <td>4,995</td>
      <td>160</td>
      <td>70</td>
      <td>559</td>
      <td>518</td>
      <td>1054</td>
      <td>426</td>
      <td>344</td>
      <td>422</td>
      <td>566</td>
      <td>876</td>
      <td>0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>9.0</td>
      <td>강북구</td>
      <td>3,321</td>
      <td>21</td>
      <td>16</td>
      <td>68</td>
      <td>210</td>
      <td>4</td>
      <td>375</td>
      <td>963</td>
      <td>569</td>
      <td>298</td>
      <td>797</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>16</th>
      <td>16.0</td>
      <td>강서구</td>
      <td>3,353</td>
      <td>132</td>
      <td>200</td>
      <td>192</td>
      <td>167</td>
      <td>507</td>
      <td>259</td>
      <td>455</td>
      <td>363</td>
      <td>441</td>
      <td>596</td>
      <td>41</td>
    </tr>
    <tr>
      <th>10</th>
      <td>10.0</td>
      <td>도봉구</td>
      <td>2,385</td>
      <td>98</td>
      <td>54</td>
      <td>27</td>
      <td>91</td>
      <td>116</td>
      <td>183</td>
      <td>195</td>
      <td>179</td>
      <td>592</td>
      <td>591</td>
      <td>259</td>
    </tr>
    <tr>
      <th>12</th>
      <td>12.0</td>
      <td>은평구</td>
      <td>4,653</td>
      <td>61</td>
      <td>332</td>
      <td>329</td>
      <td>555</td>
      <td>403</td>
      <td>635</td>
      <td>1,057</td>
      <td>288</td>
      <td>471</td>
      <td>522</td>
      <td>0</td>
    </tr>
    <tr>
      <th>23</th>
      <td>23.0</td>
      <td>강남구</td>
      <td>7,243</td>
      <td>121</td>
      <td>573</td>
      <td>826</td>
      <td>1295</td>
      <td>989</td>
      <td>744</td>
      <td>790</td>
      <td>923</td>
      <td>514</td>
      <td>468</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3.0</td>
      <td>용산구</td>
      <td>2,970</td>
      <td>87</td>
      <td>68</td>
      <td>83</td>
      <td>295</td>
      <td>491</td>
      <td>115</td>
      <td>322</td>
      <td>623</td>
      <td>422</td>
      <td>464</td>
      <td>0</td>
    </tr>
    <tr>
      <th>19</th>
      <td>19.0</td>
      <td>영등포구</td>
      <td>4,660</td>
      <td>190</td>
      <td>295</td>
      <td>360</td>
      <td>285</td>
      <td>422</td>
      <td>803</td>
      <td>153</td>
      <td>1357</td>
      <td>329</td>
      <td>450</td>
      <td>16</td>
    </tr>
    <tr>
      <th>24</th>
      <td>24.0</td>
      <td>송파구</td>
      <td>3,253</td>
      <td>149</td>
      <td>74</td>
      <td>183</td>
      <td>126</td>
      <td>227</td>
      <td>526</td>
      <td>1050</td>
      <td>237</td>
      <td>151</td>
      <td>431</td>
      <td>99</td>
    </tr>
    <tr>
      <th>25</th>
      <td>25.0</td>
      <td>강동구</td>
      <td>3,192</td>
      <td>277</td>
      <td>56</td>
      <td>174</td>
      <td>226</td>
      <td>351</td>
      <td>379</td>
      <td>362</td>
      <td>635</td>
      <td>301</td>
      <td>429</td>
      <td>2</td>
    </tr>
    <tr>
      <th>20</th>
      <td>20.0</td>
      <td>동작구</td>
      <td>2,690</td>
      <td>88</td>
      <td>478</td>
      <td>127</td>
      <td>255</td>
      <td>270</td>
      <td>299</td>
      <td>311</td>
      <td>433</td>
      <td>30</td>
      <td>373</td>
      <td>26</td>
    </tr>
    <tr>
      <th>17</th>
      <td>17.0</td>
      <td>구로구</td>
      <td>4,831</td>
      <td>307</td>
      <td>160</td>
      <td>263</td>
      <td>351</td>
      <td>559</td>
      <td>542</td>
      <td>789</td>
      <td>652</td>
      <td>689</td>
      <td>341</td>
      <td>178</td>
    </tr>
    <tr>
      <th>8</th>
      <td>8.0</td>
      <td>성북구</td>
      <td>4,957</td>
      <td>318</td>
      <td>229</td>
      <td>322</td>
      <td>594</td>
      <td>811</td>
      <td>867</td>
      <td>714</td>
      <td>253</td>
      <td>407</td>
      <td>338</td>
      <td>104</td>
    </tr>
    <tr>
      <th>6</th>
      <td>6.0</td>
      <td>동대문구</td>
      <td>2,759</td>
      <td>3</td>
      <td>5</td>
      <td>72</td>
      <td>774</td>
      <td>702</td>
      <td>201</td>
      <td>218</td>
      <td>229</td>
      <td>229</td>
      <td>317</td>
      <td>9</td>
    </tr>
    <tr>
      <th>18</th>
      <td>18.0</td>
      <td>금천구</td>
      <td>2,885</td>
      <td>NaN</td>
      <td>43</td>
      <td>350</td>
      <td>132</td>
      <td>196</td>
      <td>539</td>
      <td>366</td>
      <td>513</td>
      <td>207</td>
      <td>304</td>
      <td>235</td>
    </tr>
    <tr>
      <th>7</th>
      <td>7.0</td>
      <td>중랑구</td>
      <td>4,193</td>
      <td>555</td>
      <td>104</td>
      <td>145</td>
      <td>153</td>
      <td>170</td>
      <td>215</td>
      <td>1074</td>
      <td>976</td>
      <td>507</td>
      <td>294</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>15</th>
      <td>15.0</td>
      <td>양천구</td>
      <td>3,890</td>
      <td>369</td>
      <td>129</td>
      <td>243</td>
      <td>423</td>
      <td>564</td>
      <td>833</td>
      <td>311</td>
      <td>352</td>
      <td>374</td>
      <td>292</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>5</th>
      <td>5.0</td>
      <td>광진구</td>
      <td>3,592</td>
      <td>130</td>
      <td>176</td>
      <td>95</td>
      <td>40</td>
      <td>666</td>
      <td>460</td>
      <td>712</td>
      <td>190</td>
      <td>738</td>
      <td>273</td>
      <td>112</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4.0</td>
      <td>성동구</td>
      <td>4,175</td>
      <td>157</td>
      <td>110</td>
      <td>366</td>
      <td>281</td>
      <td>942</td>
      <td>457</td>
      <td>643</td>
      <td>485</td>
      <td>367</td>
      <td>232</td>
      <td>135</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1.0</td>
      <td>종로구</td>
      <td>1,966</td>
      <td>553</td>
      <td>103</td>
      <td>160</td>
      <td>130</td>
      <td>144</td>
      <td>150</td>
      <td>67</td>
      <td>233</td>
      <td>88</td>
      <td>207</td>
      <td>131</td>
    </tr>
    <tr>
      <th>13</th>
      <td>13.0</td>
      <td>서대문구</td>
      <td>3,538</td>
      <td>1,012</td>
      <td>114</td>
      <td>109</td>
      <td>277</td>
      <td>416</td>
      <td>246</td>
      <td>325</td>
      <td>441</td>
      <td>331</td>
      <td>174</td>
      <td>93</td>
    </tr>
    <tr>
      <th>11</th>
      <td>11.0</td>
      <td>노원구</td>
      <td>2,626</td>
      <td>238</td>
      <td>75</td>
      <td>509</td>
      <td>329</td>
      <td>171</td>
      <td>216</td>
      <td>324</td>
      <td>387</td>
      <td>215</td>
      <td>144</td>
      <td>18</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2.0</td>
      <td>중 구</td>
      <td>2,584</td>
      <td>217</td>
      <td>77</td>
      <td>236</td>
      <td>240</td>
      <td>372</td>
      <td>386</td>
      <td>155</td>
      <td>361</td>
      <td>403</td>
      <td>137</td>
      <td>0</td>
    </tr>
    <tr>
      <th>21</th>
      <td>21.0</td>
      <td>관악구</td>
      <td>5,642</td>
      <td>592</td>
      <td>277</td>
      <td>448</td>
      <td>607</td>
      <td>675</td>
      <td>663</td>
      <td>889</td>
      <td>404</td>
      <td>549</td>
      <td>135</td>
      <td>403</td>
    </tr>
    <tr>
      <th>14</th>
      <td>14.0</td>
      <td>마포구</td>
      <td>2,638</td>
      <td>61</td>
      <td>109</td>
      <td>170</td>
      <td>458</td>
      <td>376</td>
      <td>368</td>
      <td>494</td>
      <td>298</td>
      <td>177</td>
      <td>117</td>
      <td>10</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-30dbbafd-5863-44a7-ae86-6fe5319e0c4e')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-30dbbafd-5863-44a7-ae86-6fe5319e0c4e button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-30dbbafd-5863-44a7-ae86-6fe5319e0c4e');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


<div id="df-3b106601-436b-4cdb-8a91-616f2d7ab05c">
  <button class="colab-df-quickchart" onclick="quickchart('df-3b106601-436b-4cdb-8a91-616f2d7ab05c')"
            title="Suggest charts"
            style="display:none;">

<svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
     width="24px">
    <g>
        <path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zM9 17H7v-7h2v7zm4 0h-2V7h2v10zm4 0h-2v-4h2v4z"/>
    </g>
</svg>
  </button>

<style>
  .colab-df-quickchart {
      --bg-color: #E8F0FE;
      --fill-color: #1967D2;
      --hover-bg-color: #E2EBFA;
      --hover-fill-color: #174EA6;
      --disabled-fill-color: #AAA;
      --disabled-bg-color: #DDD;
  }

  [theme=dark] .colab-df-quickchart {
      --bg-color: #3B4455;
      --fill-color: #D2E3FC;
      --hover-bg-color: #434B5C;
      --hover-fill-color: #FFFFFF;
      --disabled-bg-color: #3B4455;
      --disabled-fill-color: #666;
  }

  .colab-df-quickchart {
    background-color: var(--bg-color);
    border: none;
    border-radius: 50%;
    cursor: pointer;
    display: none;
    fill: var(--fill-color);
    height: 32px;
    padding: 0;
    width: 32px;
  }

  .colab-df-quickchart:hover {
    background-color: var(--hover-bg-color);
    box-shadow: 0 1px 2px rgba(60, 64, 67, 0.3), 0 1px 3px 1px rgba(60, 64, 67, 0.15);
    fill: var(--button-hover-fill-color);
  }

  .colab-df-quickchart-complete:disabled,
  .colab-df-quickchart-complete:disabled:hover {
    background-color: var(--disabled-bg-color);
    fill: var(--disabled-fill-color);
    box-shadow: none;
  }

  .colab-df-spinner {
    border: 2px solid var(--fill-color);
    border-color: transparent;
    border-bottom-color: var(--fill-color);
    animation:
      spin 1s steps(1) infinite;
  }

  @keyframes spin {
    0% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
      border-left-color: var(--fill-color);
    }
    20% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    30% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
      border-right-color: var(--fill-color);
    }
    40% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    60% {
      border-color: transparent;
      border-right-color: var(--fill-color);
    }
    80% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-bottom-color: var(--fill-color);
    }
    90% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
    }
  }
</style>

  <script>
    async function quickchart(key) {
      const quickchartButtonEl =
        document.querySelector('#' + key + ' button');
      quickchartButtonEl.disabled = true;  // To prevent multiple clicks.
      quickchartButtonEl.classList.add('colab-df-spinner');
      try {
        const charts = await google.colab.kernel.invokeFunction(
            'suggestCharts', [key], {});
      } catch (error) {
        console.error('Error during call to suggestCharts:', error);
      }
      quickchartButtonEl.classList.remove('colab-df-spinner');
      quickchartButtonEl.classList.add('colab-df-quickchart-complete');
    }
    (() => {
      let quickchartButtonEl =
        document.querySelector('#df-3b106601-436b-4cdb-8a91-616f2d7ab05c button');
      quickchartButtonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';
    })();
  </script>
</div>

    </div>
  </div>





```python
cctv_seoul2 = cctv_seoul.copy()
```


```python
cctv_seoul2
```





  <div id="df-84021b1f-39dc-4cf4-884e-658e7ee49be6" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Unnamed: 0</th>
      <th>구분</th>
      <th>총계</th>
      <th>2014년 이전</th>
      <th>2014년</th>
      <th>2015년</th>
      <th>2016년</th>
      <th>2017년</th>
      <th>2018년</th>
      <th>2019년</th>
      <th>2020년</th>
      <th>2021년</th>
      <th>2022년</th>
      <th>2023년</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>NaN</td>
      <td>계</td>
      <td>92,991</td>
      <td>5,896</td>
      <td>3,927</td>
      <td>6,416</td>
      <td>8,812</td>
      <td>11,598</td>
      <td>10,887</td>
      <td>13,083</td>
      <td>11,803</td>
      <td>9,396</td>
      <td>9,302</td>
      <td>1,871</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1.0</td>
      <td>종로구</td>
      <td>1,966</td>
      <td>553</td>
      <td>103</td>
      <td>160</td>
      <td>130</td>
      <td>144</td>
      <td>150</td>
      <td>67</td>
      <td>233</td>
      <td>88</td>
      <td>207</td>
      <td>131</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2.0</td>
      <td>중 구</td>
      <td>2,584</td>
      <td>217</td>
      <td>77</td>
      <td>236</td>
      <td>240</td>
      <td>372</td>
      <td>386</td>
      <td>155</td>
      <td>361</td>
      <td>403</td>
      <td>137</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3.0</td>
      <td>용산구</td>
      <td>2,970</td>
      <td>87</td>
      <td>68</td>
      <td>83</td>
      <td>295</td>
      <td>491</td>
      <td>115</td>
      <td>322</td>
      <td>623</td>
      <td>422</td>
      <td>464</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4.0</td>
      <td>성동구</td>
      <td>4,175</td>
      <td>157</td>
      <td>110</td>
      <td>366</td>
      <td>281</td>
      <td>942</td>
      <td>457</td>
      <td>643</td>
      <td>485</td>
      <td>367</td>
      <td>232</td>
      <td>135</td>
    </tr>
    <tr>
      <th>5</th>
      <td>5.0</td>
      <td>광진구</td>
      <td>3,592</td>
      <td>130</td>
      <td>176</td>
      <td>95</td>
      <td>40</td>
      <td>666</td>
      <td>460</td>
      <td>712</td>
      <td>190</td>
      <td>738</td>
      <td>273</td>
      <td>112</td>
    </tr>
    <tr>
      <th>6</th>
      <td>6.0</td>
      <td>동대문구</td>
      <td>2,759</td>
      <td>3</td>
      <td>5</td>
      <td>72</td>
      <td>774</td>
      <td>702</td>
      <td>201</td>
      <td>218</td>
      <td>229</td>
      <td>229</td>
      <td>317</td>
      <td>9</td>
    </tr>
    <tr>
      <th>7</th>
      <td>7.0</td>
      <td>중랑구</td>
      <td>4,193</td>
      <td>555</td>
      <td>104</td>
      <td>145</td>
      <td>153</td>
      <td>170</td>
      <td>215</td>
      <td>1074</td>
      <td>976</td>
      <td>507</td>
      <td>294</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>8</th>
      <td>8.0</td>
      <td>성북구</td>
      <td>4,957</td>
      <td>318</td>
      <td>229</td>
      <td>322</td>
      <td>594</td>
      <td>811</td>
      <td>867</td>
      <td>714</td>
      <td>253</td>
      <td>407</td>
      <td>338</td>
      <td>104</td>
    </tr>
    <tr>
      <th>9</th>
      <td>9.0</td>
      <td>강북구</td>
      <td>3,321</td>
      <td>21</td>
      <td>16</td>
      <td>68</td>
      <td>210</td>
      <td>4</td>
      <td>375</td>
      <td>963</td>
      <td>569</td>
      <td>298</td>
      <td>797</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>10</th>
      <td>10.0</td>
      <td>도봉구</td>
      <td>2,385</td>
      <td>98</td>
      <td>54</td>
      <td>27</td>
      <td>91</td>
      <td>116</td>
      <td>183</td>
      <td>195</td>
      <td>179</td>
      <td>592</td>
      <td>591</td>
      <td>259</td>
    </tr>
    <tr>
      <th>11</th>
      <td>11.0</td>
      <td>노원구</td>
      <td>2,626</td>
      <td>238</td>
      <td>75</td>
      <td>509</td>
      <td>329</td>
      <td>171</td>
      <td>216</td>
      <td>324</td>
      <td>387</td>
      <td>215</td>
      <td>144</td>
      <td>18</td>
    </tr>
    <tr>
      <th>12</th>
      <td>12.0</td>
      <td>은평구</td>
      <td>4,653</td>
      <td>61</td>
      <td>332</td>
      <td>329</td>
      <td>555</td>
      <td>403</td>
      <td>635</td>
      <td>1,057</td>
      <td>288</td>
      <td>471</td>
      <td>522</td>
      <td>0</td>
    </tr>
    <tr>
      <th>13</th>
      <td>13.0</td>
      <td>서대문구</td>
      <td>3,538</td>
      <td>1,012</td>
      <td>114</td>
      <td>109</td>
      <td>277</td>
      <td>416</td>
      <td>246</td>
      <td>325</td>
      <td>441</td>
      <td>331</td>
      <td>174</td>
      <td>93</td>
    </tr>
    <tr>
      <th>14</th>
      <td>14.0</td>
      <td>마포구</td>
      <td>2,638</td>
      <td>61</td>
      <td>109</td>
      <td>170</td>
      <td>458</td>
      <td>376</td>
      <td>368</td>
      <td>494</td>
      <td>298</td>
      <td>177</td>
      <td>117</td>
      <td>10</td>
    </tr>
    <tr>
      <th>15</th>
      <td>15.0</td>
      <td>양천구</td>
      <td>3,890</td>
      <td>369</td>
      <td>129</td>
      <td>243</td>
      <td>423</td>
      <td>564</td>
      <td>833</td>
      <td>311</td>
      <td>352</td>
      <td>374</td>
      <td>292</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>16</th>
      <td>16.0</td>
      <td>강서구</td>
      <td>3,353</td>
      <td>132</td>
      <td>200</td>
      <td>192</td>
      <td>167</td>
      <td>507</td>
      <td>259</td>
      <td>455</td>
      <td>363</td>
      <td>441</td>
      <td>596</td>
      <td>41</td>
    </tr>
    <tr>
      <th>17</th>
      <td>17.0</td>
      <td>구로구</td>
      <td>4,831</td>
      <td>307</td>
      <td>160</td>
      <td>263</td>
      <td>351</td>
      <td>559</td>
      <td>542</td>
      <td>789</td>
      <td>652</td>
      <td>689</td>
      <td>341</td>
      <td>178</td>
    </tr>
    <tr>
      <th>18</th>
      <td>18.0</td>
      <td>금천구</td>
      <td>2,885</td>
      <td>NaN</td>
      <td>43</td>
      <td>350</td>
      <td>132</td>
      <td>196</td>
      <td>539</td>
      <td>366</td>
      <td>513</td>
      <td>207</td>
      <td>304</td>
      <td>235</td>
    </tr>
    <tr>
      <th>19</th>
      <td>19.0</td>
      <td>영등포구</td>
      <td>4,660</td>
      <td>190</td>
      <td>295</td>
      <td>360</td>
      <td>285</td>
      <td>422</td>
      <td>803</td>
      <td>153</td>
      <td>1357</td>
      <td>329</td>
      <td>450</td>
      <td>16</td>
    </tr>
    <tr>
      <th>20</th>
      <td>20.0</td>
      <td>동작구</td>
      <td>2,690</td>
      <td>88</td>
      <td>478</td>
      <td>127</td>
      <td>255</td>
      <td>270</td>
      <td>299</td>
      <td>311</td>
      <td>433</td>
      <td>30</td>
      <td>373</td>
      <td>26</td>
    </tr>
    <tr>
      <th>21</th>
      <td>21.0</td>
      <td>관악구</td>
      <td>5,642</td>
      <td>592</td>
      <td>277</td>
      <td>448</td>
      <td>607</td>
      <td>675</td>
      <td>663</td>
      <td>889</td>
      <td>404</td>
      <td>549</td>
      <td>135</td>
      <td>403</td>
    </tr>
    <tr>
      <th>22</th>
      <td>22.0</td>
      <td>서초구</td>
      <td>4,995</td>
      <td>160</td>
      <td>70</td>
      <td>559</td>
      <td>518</td>
      <td>1054</td>
      <td>426</td>
      <td>344</td>
      <td>422</td>
      <td>566</td>
      <td>876</td>
      <td>0</td>
    </tr>
    <tr>
      <th>23</th>
      <td>23.0</td>
      <td>강남구</td>
      <td>7,243</td>
      <td>121</td>
      <td>573</td>
      <td>826</td>
      <td>1295</td>
      <td>989</td>
      <td>744</td>
      <td>790</td>
      <td>923</td>
      <td>514</td>
      <td>468</td>
      <td>0</td>
    </tr>
    <tr>
      <th>24</th>
      <td>24.0</td>
      <td>송파구</td>
      <td>3,253</td>
      <td>149</td>
      <td>74</td>
      <td>183</td>
      <td>126</td>
      <td>227</td>
      <td>526</td>
      <td>1050</td>
      <td>237</td>
      <td>151</td>
      <td>431</td>
      <td>99</td>
    </tr>
    <tr>
      <th>25</th>
      <td>25.0</td>
      <td>강동구</td>
      <td>3,192</td>
      <td>277</td>
      <td>56</td>
      <td>174</td>
      <td>226</td>
      <td>351</td>
      <td>379</td>
      <td>362</td>
      <td>635</td>
      <td>301</td>
      <td>429</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-84021b1f-39dc-4cf4-884e-658e7ee49be6')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-84021b1f-39dc-4cf4-884e-658e7ee49be6 button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-84021b1f-39dc-4cf4-884e-658e7ee49be6');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


<div id="df-0f20c320-9e26-4cae-a7bb-55557fc1fd80">
  <button class="colab-df-quickchart" onclick="quickchart('df-0f20c320-9e26-4cae-a7bb-55557fc1fd80')"
            title="Suggest charts"
            style="display:none;">

<svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
     width="24px">
    <g>
        <path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zM9 17H7v-7h2v7zm4 0h-2V7h2v10zm4 0h-2v-4h2v4z"/>
    </g>
</svg>
  </button>

<style>
  .colab-df-quickchart {
      --bg-color: #E8F0FE;
      --fill-color: #1967D2;
      --hover-bg-color: #E2EBFA;
      --hover-fill-color: #174EA6;
      --disabled-fill-color: #AAA;
      --disabled-bg-color: #DDD;
  }

  [theme=dark] .colab-df-quickchart {
      --bg-color: #3B4455;
      --fill-color: #D2E3FC;
      --hover-bg-color: #434B5C;
      --hover-fill-color: #FFFFFF;
      --disabled-bg-color: #3B4455;
      --disabled-fill-color: #666;
  }

  .colab-df-quickchart {
    background-color: var(--bg-color);
    border: none;
    border-radius: 50%;
    cursor: pointer;
    display: none;
    fill: var(--fill-color);
    height: 32px;
    padding: 0;
    width: 32px;
  }

  .colab-df-quickchart:hover {
    background-color: var(--hover-bg-color);
    box-shadow: 0 1px 2px rgba(60, 64, 67, 0.3), 0 1px 3px 1px rgba(60, 64, 67, 0.15);
    fill: var(--button-hover-fill-color);
  }

  .colab-df-quickchart-complete:disabled,
  .colab-df-quickchart-complete:disabled:hover {
    background-color: var(--disabled-bg-color);
    fill: var(--disabled-fill-color);
    box-shadow: none;
  }

  .colab-df-spinner {
    border: 2px solid var(--fill-color);
    border-color: transparent;
    border-bottom-color: var(--fill-color);
    animation:
      spin 1s steps(1) infinite;
  }

  @keyframes spin {
    0% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
      border-left-color: var(--fill-color);
    }
    20% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    30% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
      border-right-color: var(--fill-color);
    }
    40% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    60% {
      border-color: transparent;
      border-right-color: var(--fill-color);
    }
    80% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-bottom-color: var(--fill-color);
    }
    90% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
    }
  }
</style>

  <script>
    async function quickchart(key) {
      const quickchartButtonEl =
        document.querySelector('#' + key + ' button');
      quickchartButtonEl.disabled = true;  // To prevent multiple clicks.
      quickchartButtonEl.classList.add('colab-df-spinner');
      try {
        const charts = await google.colab.kernel.invokeFunction(
            'suggestCharts', [key], {});
      } catch (error) {
        console.error('Error during call to suggestCharts:', error);
      }
      quickchartButtonEl.classList.remove('colab-df-spinner');
      quickchartButtonEl.classList.add('colab-df-quickchart-complete');
    }
    (() => {
      let quickchartButtonEl =
        document.querySelector('#df-0f20c320-9e26-4cae-a7bb-55557fc1fd80 button');
      quickchartButtonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';
    })();
  </script>
</div>

  <div id="id_0bae17e8-b217-4563-bdfb-095a37ca4db5">
    <style>
      .colab-df-generate {
        background-color: #E8F0FE;
        border: none;
        border-radius: 50%;
        cursor: pointer;
        display: none;
        fill: #1967D2;
        height: 32px;
        padding: 0 0 0 0;
        width: 32px;
      }

      .colab-df-generate:hover {
        background-color: #E2EBFA;
        box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
        fill: #174EA6;
      }

      [theme=dark] .colab-df-generate {
        background-color: #3B4455;
        fill: #D2E3FC;
      }

      [theme=dark] .colab-df-generate:hover {
        background-color: #434B5C;
        box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
        filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
        fill: #FFFFFF;
      }
    </style>
    <button class="colab-df-generate" onclick="generateWithVariable('cctv_seoul2')"
            title="Generate code using this dataframe."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M7,19H8.4L18.45,9,17,7.55,7,17.6ZM5,21V16.75L18.45,3.32a2,2,0,0,1,2.83,0l1.4,1.43a1.91,1.91,0,0,1,.58,1.4,1.91,1.91,0,0,1-.58,1.4L9.25,21ZM18.45,9,17,7.55Zm-12,3A5.31,5.31,0,0,0,4.9,8.1,5.31,5.31,0,0,0,1,6.5,5.31,5.31,0,0,0,4.9,4.9,5.31,5.31,0,0,0,6.5,1,5.31,5.31,0,0,0,8.1,4.9,5.31,5.31,0,0,0,12,6.5,5.46,5.46,0,0,0,6.5,12Z"/>
  </svg>
    </button>
    <script>
      (() => {
      const buttonEl =
        document.querySelector('#id_0bae17e8-b217-4563-bdfb-095a37ca4db5 button.colab-df-generate');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      buttonEl.onclick = () => {
        google.colab.notebook.generateWithVariable('cctv_seoul2');
      }
      })();
    </script>
  </div>

    </div>
  </div>





```python
cctv_seoul[cctv_seoul2.구분 = '계']
```


      File "<ipython-input-201-d378047ffe1a>", line 1
        cctv_seoul[cctv_seoul2.구분 = '계']
                   ^
    SyntaxError: cannot assign to attribute here. Maybe you meant '==' instead of '='?




```python

```
