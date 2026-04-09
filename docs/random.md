# Random number picker

<div id="random-picker" style="max-width: 720px;">
  <div>
    <label for="rp-all"><strong>All numbers</strong></label><br>
    <textarea id="rp-all" rows="6" style="width:100%;">101
102
104
105
106
107
109
110
111
112
113
114
115
116
117
118
119
120
121
122
123
125
126
127
129
130
131
132
133
134
135
136
137
138
139
140
141
142
143
144
145
146
147
148
149
150
151
152
153
154
155
156
157
158
159
160
161
162
163
164
166
167
168
169
170
171
172
173
174
175
176
177
178
179
180
181
182
184
185
186
188
189
190
191
192
193
194
196
197
198
199
680</textarea>
    <div style="font-size: 0.9em; opacity: 0.8;">
      Use commas, spaces, or new lines.
    </div>
  </div>

  <div style="margin-top: 1rem;">
    <label for="rp-default"><strong>Predefined numbers</strong></label><br>
    <textarea id="rp-default" rows="3" style="width:100%;">680
161</textarea>
    <div style="font-size: 0.9em; opacity: 0.8;">
      These are included by default before random picks are added.
    </div>
  </div>

  <div style="margin-top: 1rem;">
    <label for="rp-n"><strong>N</strong></label><br>
    <input id="rp-n" type="number" min="1" value="46" style="width:120px;">
  </div>

  <div style="margin-top: 1rem;">
    <label for="rp-seed"><strong>Random seed</strong></label><br>
    <input id="rp-seed" type="text" value="314159" style="width:220px;">
  </div>

  <div style="margin-top: 1rem;">
    <button id="rp-btn" type="button">Pick numbers</button>
  </div>

  <div style="margin-top: 1rem;">
    <strong>Selected values</strong>
    <div
      id="rp-result"
      style="
        margin-top: 0.5rem;
        padding: 0.75rem;
        border: 1px solid rgba(127,127,127,0.35);
        border-radius: 8px;
        min-height: 3rem;
        max-height: 16rem;
        overflow-y: auto;
        white-space: normal;
        word-break: break-word;
        line-height: 1.8;
      "
    >-</div>
  </div>

  <div id="rp-message" style="margin-top: 0.5rem;"></div>
</div>

<script>
(function () {
  function parseNumbers(text) {
    return [...new Set(
      text
        .split(/[\s,]+/)
        .map(s => s.trim())
        .filter(Boolean)
        .map(Number)
        .filter(n => !Number.isNaN(n))
    )];
  }

  function hashString(str) {
    let h = 2166136261;
    for (let i = 0; i < str.length; i++) {
      h ^= str.charCodeAt(i);
      h = Math.imul(h, 16777619);
    }
    return h >>> 0;
  }

  function mulberry32(seed) {
    let a = seed >>> 0;
    return function () {
      a += 0x6D2B79F5;
      let t = a;
      t = Math.imul(t ^ (t >>> 15), t | 1);
      t ^= t + Math.imul(t ^ (t >>> 7), t | 61);
      return ((t ^ (t >>> 14)) >>> 0) / 4294967296;
    };
  }

  function shuffle(array, randomFn) {
    const arr = [...array];
    for (let i = arr.length - 1; i > 0; i--) {
      const j = Math.floor(randomFn() * (i + 1));
      [arr[i], arr[j]] = [arr[j], arr[i]];
    }
    return arr;
  }

  function initRandomPicker() {
    const container = document.getElementById("random-picker");
    if (!container || container.dataset.bound === "true") return;
    container.dataset.bound = "true";

    const allInput = document.getElementById("rp-all");
    const defaultInput = document.getElementById("rp-default");
    const nInput = document.getElementById("rp-n");
    const seedInput = document.getElementById("rp-seed");
    const button = document.getElementById("rp-btn");
    const result = document.getElementById("rp-result");
    const message = document.getElementById("rp-message");

    button.addEventListener("click", function () {
      const allNumbers = parseNumbers(allInput.value);
      const predefined = parseNumbers(defaultInput.value);
      const n = Number(nInput.value);
      const seedText = seedInput.value.trim();

      result.textContent = "-";
      message.textContent = "";

      if (!Number.isInteger(n) || n < 1) {
        message.textContent = "N must be a positive integer.";
        return;
      }

      if (allNumbers.length === 0) {
        message.textContent = "Please provide at least one number in 'All numbers'.";
        return;
      }

      if (n > allNumbers.length) {
        message.textContent = "N is larger than the number of available values.";
        return;
      }

      const allSet = new Set(allNumbers);
      const validPredefined = predefined.filter(x => allSet.has(x));
      const invalidPredefined = predefined.filter(x => !allSet.has(x));

      if (validPredefined.length > n) {
        message.textContent = "The number of predefined values is greater than N.";
        return;
      }

      const seedValue = hashString(seedText || "default-seed");
      const randomFn = mulberry32(seedValue);

      const remainingPool = allNumbers.filter(x => !validPredefined.includes(x));
      const remainingNeeded = n - validPredefined.length;
      const randomPart = shuffle(remainingPool, randomFn).slice(0, remainingNeeded);

      const finalSelection = [...validPredefined, ...randomPart]
        .sort((a, b) => a - b);

      result.textContent = finalSelection.join(", ");

      if (invalidPredefined.length > 0) {
        message.textContent =
          "Ignored predefined values not found in the main list: " +
          invalidPredefined.join(", ");
      }
    });
  }

  if (document.readyState === "loading") {
    document.addEventListener("DOMContentLoaded", initRandomPicker);
  } else {
    initRandomPicker();
  }
})();
</script>
