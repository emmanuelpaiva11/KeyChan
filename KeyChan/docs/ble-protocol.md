# KeyChan BLE Protocol

## Service
- **UUID:** `0000ffe0-0000-1000-8000-00805f9b34fb`

## Characteristics

### Write Characteristic (Image Data)
- **UUID:** `0000ffe1-0000-1000-8000-00805f9b34fb`
- **Properties:** Write, Write Without Response
- **Max Length:** 20 bytes (BLE MTU - 3)

### Protocol

1. **Start Transfer** — Send `0xAA 0xAA 0xAA 0xAA` (4 bytes)
2. **Image Header** — Send 8 bytes:
   - Width (2 bytes, little-endian)
   - Height (2 bytes, little-endian)
   - Format (1 byte): 0=raw 1bpp, 1=raw 2bpp, 2=compressed
   - Flags (1 byte): bit 0 = full refresh, bit 1 = partial
   - Checksum (2 bytes, little-endian, CRC16)
3. **Image Data** — Send in 20-byte chunks until complete
4. **End Transfer** — Send `0xBB 0xBB 0xBB 0xBB` (4 bytes)

### Response
- Device responds with `0xCC` (success) or `0xEE` (error) after end transfer

## Android Example
```java
// Connect to KeyChan
BluetoothDevice device = /* ... */;
BluetoothGatt gatt = device.connectGatt(this, false, callback);

// Find service
BluetoothGattService service = gatt.getService(UUID.fromString("0000ffe0-0000-1000-8000-00805f9b34fb"));
BluetoothGattCharacteristic char = service.getCharacteristic(UUID.fromString("0000ffe1-0000-1000-8000-00805f9b34fb"));

// Send image header + data...
char.setValue(data);
gatt.writeCharacteristic(char);
```

## Web Example
```javascript
const serviceUUID = '0000ffe0-0000-1000-8000-00805f9b34fb';
const charUUID = '0000ffe1-0000-1000-8000-00805f9b34fb';

const device = await navigator.bluetooth.requestDevice({ filters: [{ services: [serviceUUID] }] });
const server = await device.gatt.connect();
const service = await server.getPrimaryService(serviceUUID);
const characteristic = await service.getCharacteristic(charUUID);
await characteristic.writeValue(new Uint8Array([0xAA, 0xAA, 0xAA, 0xAA]));
```