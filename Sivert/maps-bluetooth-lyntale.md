---
title: "Bluetooth fra nettlesern"
subtitle: "Hvordan kommunisere med duppeditter fra nettlesern over Bluetooth."
output: beamer_presentation
theme: metropolis
---

# Bluetooth Low Energy (BLE)

_Snacksy standard for kommunikasjon med duppeditter._

# Connect til en duppeditts tjenester

\scriptsize

```js
const device = await navigator.bluetooth.requestDevice({
  filters: [{ services: ["heart_rate"] }],
});

const server = await device.gatt.connect();
console.log("Server connected:", server);

const heartRateService = await server.getPrimaryService("heart_rate");
console.log("Heart rate service connected:", server);
```

\normalsize

# Les en tjenestes verdi

\tiny

```js
const heartRateCharacteristic = await heartRateService.getCharacteristic(
  "heart_rate_measurement"
);
{
  oncharacteristicvaluechanged: null,
  properties: {
    authenticatedSignedWrites: false,
    broadcast: false,
    indicate: false,
    notify: true,
    read: false,
    reliableWrite: false,
    writableAuxiliaries: false,
    write: false,
    writeWithoutResponse: false,
  },
  service: {
    device: BluetoothDevice,
    uuid: '0000180d-0000-1000-8000-00805f9b34fb',
    isPrimary: true
  },
  uuid: "00002a37-0000-1000-8000-00805f9b34fb",
  value: null,
}
```

\normalsize

# Les en tjenestes verdi

\scriptsize

```js
const heartRateCharacteristic = await heartRateService.getCharacteristic(
  "heart_rate_measurement"
);

// Lytt etter varsler
heartRateCharacteristic.addEventListener(
  "characteristicvaluechanged",
  (event) => {
    const rawValue = event.target.value;
    // rawValue er et DataView aka mer eller mindre rå byte-data
    const hr = rawValue.getUint8(1);
    console.log("Puls:", hr);
  }
);
// Gi oss varsler når verdien endres
heartRateCharacteristic.startNotifications();
```

\normalsize

# Hele greia

\tiny

```js
const device = await navigator.bluetooth.requestDevice({
  filters: [{ services: ["heart_rate"] }],
});
const server = await device.gatt.connect();
console.log("Server connected:", server);
const heartRateService = await server.getPrimaryService("heart_rate");
console.log("Heart rate service connected:", server);
const heartRateCharacteristic = await heartRateService.getCharacteristic(
  "heart_rate_measurement"
);
console.log("heartRateCharacteristic:", heartRateCharacteristic);
// Lytt etter varsler
heartRateCharacteristic.addEventListener(
  "characteristicvaluechanged",
  (event) => {
    const rawValue = event.target.value;
    // rawValue er et DataView aka mer eller mindre rå byte-data
    const hr = rawValue.getUint8(1);
    console.log("Puls:", hr);
  }
);
// Gi oss varsler når verdien endres
heartRateCharacteristic.startNotifications();
```

\normalsize

[HR-service-spec](https://www.bluetooth.com/specifications/specs/heart-rate-service-1-0/)

# Hvorfor er det fett at man kan gjøre dette gjennom nettlesern?

- Lavere terskel for interaksjon med duppeditter?
