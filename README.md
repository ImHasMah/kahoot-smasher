Sure! Here’s how you can input your modified code into GitHub and deploy it using GitHub Pages:

1. **Fork the Repository:**
   - Go to [ThatFruedDued/kahoot-smasher](https://github.com/ThatFruedDued/kahoot-smasher).
   - Click the "Fork" button at the top right to create a copy of the repository under your GitHub account.

2. **Clone the Repository:**
   - On your forked repository page, click the "Code" button and copy the URL.
   - Open a terminal or command prompt and run the following command to clone the repository to your local machine:
     ```bash
     git clone https://github.com/YOUR_USERNAME/kahoot-smasher.git
     ```
   - Replace `YOUR_USERNAME` with your GitHub username.

3. **Add HTML and JavaScript Files:**
   - Navigate to the cloned repository directory:
     ```bash
     cd kahoot-smasher
     ```
   - Create a new `index.html` file with the following content:
     ```html
     <!DOCTYPE html>
     <html lang="en">
     <head>
         <meta charset="UTF-8">
         <meta name="viewport" content="width=device-width, initial-scale=1.0">
         <title>Kahoot Smasher</title>
     </head>
     <body>
         <h1>Kahoot Smasher</h1>
         <form id="smasherForm">
             <label for="gamePin">Game Pin:</label>
             <input type="text" id="gamePin" name="gamePin" required><br><br>
             <label for="botCount">Number of Bots:</label>
             <input type="number" id="botCount" name="botCount" required><br><br>
             <button type="submit">Start</button>
         </form>
     
         <script>
             document.getElementById('smasherForm').addEventListener('submit', function(event) {
                 event.preventDefault(); // Prevents the form from submitting the traditional way
                 const gamePin = document.getElementById('gamePin').value;
                 const botCount = document.getElementById('botCount').value;
                 runSmasher(gamePin, botCount);
             });
     
             function runSmasher(pin, bots) {
                 // Your existing JavaScript code here
                 var O, G = new Uint8Array(16);
                 function W() {
                     if (!O) {
                         if (O = typeof crypto !== "undefined" && crypto.getRandomValues && crypto.getRandomValues.bind(crypto), !O)
                             throw new Error("crypto.getRandomValues() not supported. See https://github.com/uuidjs/uuid#getrandomvalues-not-supported");
                     }
                     return O(G);
                 }
                 function k(U, D = 0) {
                     return p[U[D + 0]] + p[U[D + 1]] + p[U[D + 2]] + p[U[D + 3]] + "-" +
                            p[U[D + 4]] + p[U[D + 5]] + "-" + p[U[D + 6]] + p[U[D + 7]] + "-" +
                            p[U[D + 8]] + p[U[D + 9]] + "-" + p[U[D + 10]] + p[U[D + 11]] +
                            p[U[D + 12]] + p[U[D + 13]] + p[U[D + 14]] + p[U[D + 15]];
                 }
                 var p = [];
                 for (let U = 0; U < 256; ++U)
                     p.push((U + 256).toString(16).slice(1));
                 var L = typeof crypto !== "undefined" && crypto.randomUUID && crypto.randomUUID.bind(crypto),
                     X = {randomUUID: L};
                 var S = function (U, D, A) {
                     if (X.randomUUID && !D && !U) return X.randomUUID();
                     U = U || {};
                     const E = U.random || (U.rng || W)();
                     if (E[6] = E[6] & 15 | 64, E[8] = E[8] & 63 | 128, D) {
                         A = A || 0;
                         for (let q = 0; q < 16; ++q) D[A + q] = E[q];
                         return D;
                     }
                     return k(E);
                 }, Y = S;
                 async function B(pin, name) {
                     const generatedUuid = Y();
                     if (Z) document.cookie = `generated-uuid=${generatedUuid}`;
                     const reserveResponse = await fetch(`https://kahoot.it/reserve/session/${pin}/?${Date.now()}`, Z ? void 0 : {
                             headers: {cookie: `generated-uuid=${generatedUuid}`}
                         }),
                         sessionToken = reserveResponse.headers.get("x-kahoot-session-token"),
                         {challenge} = await reserveResponse.json(),
                         challengeInput = challenge.slice(19, 119),
                         offsetCode = challenge.split("var offset = ")[1].split(";")[0].replace(/\s/g, "");
                     if (/[^\d()*+]/.test(offsetCode)) {
                         console.warn("Kahoot just attempted to run the following code on your computer:", offsetCode, "\nLuckily, you were protected.");
                         return;
                     }
                     const offset = eval(offsetCode),
                         challengeResponse = challengeInput.split("").map((U, D) => String.fromCharCode((U.charCodeAt(0) * D + offset) % 77 + 48)).join(""),
                         xorResult = String.fromCharCode(...atob(sessionToken).split("").map((U, D) => U.charCodeAt(0) ^ challengeResponse.charCodeAt(D))),
                         ws = new WebSocket(`wss://kahoot.it/cometd/${pin}/${xorResult}`, Z ? void 0 : {headers: {cookie: `generated-uuid=${generatedUuid}`}});
                     ws.onopen = async () => {
                         let U = 1;
                         const D = (z) => {
                             U++, F && console.log("Sent", z), ws.send(JSON.stringify(z));
                         };
                         ws.addEventListener("message", (z) => {
                             F && console.log("Received", JSON.parse(z.data));
                         });
                         const A = (z) => new Promise((P) => {
                             const J = (N) => {
                                 N = N;
                                 const K = JSON.parse(N.data);
                                 if (!z || z(K)) P(K), ws.removeEventListener("message", J);
                             };
                             ws.addEventListener("message", J);
                         });
                         D([{
                             id: U.toString(),
                             version: "1.0",
                             minimumVersion: "1.0",
                             channel: "/meta/handshake",
                             supportedConnectionTypes: ["websocket", "long-polling", "callback-polling"],
                             advice: {timeout: 60000, interval: 0},
                             ext: {ack: !0, timesync: {tc: Date.now(), l: 0, o: 0}}
                         }]);
                         const E = await A((z) => z?.[0]?.clientId), {clientId: q} = E[0];
                         D([{
                             id: U.toString(),
                             channel: "/meta/connect",
                             connectionType: "websocket",
                             advice: {timeout: 0},
                             clientId: q,
                             ext: {ack: 0, timesync: {tc: Date.now(), l: 0, o: 0}}
                         }]), ws.addEventListener("message", async (z) => {
                             const P = z.data, J = JSON.parse(P);
                             if (J?.[0]?.channel === "/meta/connect") D([{
                                 id: U.toString(),
                                 channel: "/meta/connect",
                                 connectionType: "websocket",
                                 clientId: q,
                                 ext: {ack: J[0].ext.ack}
                             }]);
                             if (J?.[0]?.channel === "/service/player" && J[0].data.id === 2) {
                                 await new Promise((K) => setTimeout(K, Math.floor(Math.random() * 3000)));
                                 const N = JSON.parse(J[0].data.content);
                                 if (N.type === "quiz") D([{
                                     id: U.toString(),
                                     channel: "/service/controller",
                                     data: {
                                         gameid: pin,
                                         type: "message",
                                         host: "kahoot.it",
                                         id: 45,
                                         content: JSON.stringify({
                                             type: "quiz",
                                             choice: Math.floor(Math.random() * N.numberOfChoices),
                                             questionIndex: N.gameBlockIndex
                                         })
                                     }, clientId: q, ext: {}
                                 }]); else if (N.type === "multiple_select_quiz") D([{
                                     id: U.toString(),
                                     channel: "/service/controller",
                                     data: {
                                         gameid: pin,
                                         type: "message",
                                         host: "kahoot.it",
                                         id: 45,
                                         content: JSON.stringify({
                                             type: "multiple_select_quiz",
                                             choice: new Array(N.numberOfChoices).fill(0).map((K, Q) => Q).filter(() => Math.random() < 0.5),
                                             questionIndex: N.gameBlockIndex
                                         })
                                     }, clientId: q, ext: {}
                                 }]);














# ThatFruedDued Kahoot Smasher






A little script you can use to flood Kahoot games with numerous players that randomly answer questions. Fully functional as of **August 18, 2024**.

## Usage

### Browser

To run this program in your web browser, you can either use a bookmarklet or the Developer Tools console. Developer Tools are often blocked on school devices, so you may find more success with the bookmarklet method.

#### Variants

You can either choose to run the [`index.js`](https://github.com/ThatFruedDued/kahoot-smasher/blob/master/min/index.js) script or the [`fakerless.js`](https://github.com/ThatFruedDued/kahoot-smasher/blob/master/min/fakerless.js) script. The `index.js` script includes [Faker](https://fakerjs.dev/), a tool to generate realistic names. However, the inclusion of Faker makes the script much larger, so it does not work as a bookmarklet. The `fakerless.js` script does not include Faker which reduces file size, but the generated names will just be random numbers.

#### Bookmarklet

0. Copy the contents of the [`fakerless.bookmarklet.js`](https://github.com/ThatFruedDued/kahoot-smasher/blob/master/min/fakerless.bookmarklet.js).
1. Create a new bookmark.
2. Set the name to `Kahoot Smasher`.
3. Paste the script you copied earlier into the URL field.
4. Save the bookmark.
5. After you enter a Kahoot game, click on the bookmarklet and the script will run.

#### Developer Tools

0. Copy your desired script.
1. Enter your Kahoot game.
2. Press <kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>i</kbd> on Windows/Chrome OS/Linux or <kbd>⌘</kbd> + <kbd>⌥</kbd> + <kbd>i</kbd> on macOS to open the Inspect tab.
3. At the top of the Developer Tools window, click Console.
4. Paste the script. If the script does not appear and you see a warning telling you to allow pasting, type `allow pasting` into the console and press <kbd>Enter</kbd>. Then, paste the script again.
5. Press <kbd>Enter</kbd> to run the script.
6. You can close the console window once you have run the script.

### Bun

This program was written using [Bun](https://bun.sh). However, it should also be fully compatible with comparable JavaScript runtimes such as [Deno](https://deno.land).

To install dependencies:

```bash
bun install
```

To run:

```bash
bun run start
```

## Confused?

Here's a simple tutorial to run the program using [Repl](https://repl.it), a free development platform that provides access to the tools necessary to run this program.

0. Go to [Repl](https://repl.it) and create an account (or sign in)
1. Create a new Repl, import from GitHub URL
2. Paste the link to this repository (https://github.com/ThatFruedDued/kahoot-smasher)
3. Wait for the Repl to be created and run it
4. Follow the prompts in the command window

## Licence

This code is free and open source, licenced under the GNU Affero General Public License version 3. If you are simply using the script yourself, you are not at risk of violating the license.
