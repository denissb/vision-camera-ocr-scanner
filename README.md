# vision-camera-ocr-scanner

VisionCamera Frame Processor Plugin to read barcodes and Text using MLKit Vision Barcode Scanning

This is a combination of [vision-camera-ocr](https://github.com/aarongrider/vision-camera-ocr) and [vision-camera-ocr-scanner](https://github.com/rodgomesc/vision-camera-code-scanner) in one package. Kudos to the original authors!

## Installation

```sh
yarn add vision-camera-ocr-scanner
```

make sure you correctly [setup](https://docs.swmansion.com/react-native-reanimated/docs/fundamentals/installation/) react-native-reanimated and insert as a first line of your [`index.tsx`](https://github.com/rodgomesc/vision-camera-code-scanner/blob/1409a8afd02328a26e336036493b2d6ef8441359/example/index.tsx#L1)

```sh
import 'react-native-reanimated'
```

Add this to your `babel.config.js` if you want to ue both text and barcode scanning, otherwise pick one.

```
[
  'react-native-reanimated/plugin',
  {
    globals: ['__scanCodes', '__scanOCR'],
  },
]
```

## Usage

Simply call the `useScanBarcodes()` hook or call `scanBarcodes()` inside of the `useFrameProcessor()` hook. In both cases you will need to pass an array of `BarcodeFormat` to specify the kind of barcode you want to detect.

> Note: The underlying MLKit barcode reader is only created once meaning that changes to the array will not be reflected in the app.

```js
import * as React from 'react';

import { StyleSheet, Text } from 'react-native';
import { useCameraDevices } from 'react-native-vision-camera';
import { Camera } from 'react-native-vision-camera';
import { useScanBarcodes, useScanOCR, scanOCR, scanBarcodes BarcodeFormat } from 'vision-camera-ocr-scanner';

export default function App() {
  const [hasPermission, setHasPermission] = React.useState(false);
  const [barcodes, setBarcodes] = React.useState();
  const [ocrResult, setOcrResult] = React.useState();
  const devices = useCameraDevices();
  const device = devices.back;

  const [frameProcessor, barcodes] = useScanBarcodes([BarcodeFormat.QR_CODE], {
    checkInverted: true,
  });

  // You can use the following code to scan for OCR text
  //
  // const [frameProcessor, ocrData] = useScanOCR();
  // React.useEffect(() => {
  //   // This effect will run every time the frameprocessor gets different data.
  //   if (ocrData) {
  //     console.log('Got Data: ', ocrData);
  //   }
  // }, [ocrData]);

  // Alternatively you can use the underlying function:
  //
  // const frameProcessor = useFrameProcessor((frame) => {
  //   'worklet';
  //   const detectedBarcodes = scanBarcodes(frame, [BarcodeFormat.QR_CODE], { checkInverted: true });
  //   const scannedOcr = scanOCR(frame);
  //   runOnJS(setBarcodes)(detectedBarcodes);
  //   runOnJS(setOcrResult)(scannedOcr.result);
  // }, []);

  React.useEffect(() => {
    (async () => {
      const status = await Camera.requestCameraPermission();
      setHasPermission(status === 'authorized');
    })();
  }, []);

  return (
    device != null &&
    hasPermission && (
      <>
        <Camera
          style={StyleSheet.absoluteFill}
          device={device}
          isActive={true}
          frameProcessor={frameProcessor}
          frameProcessorFps={5}
        />
        {barcodes.map((barcode, idx) => (
          <Text key={idx} style={styles.barcodeTextURL}>
            {barcode.displayValue}
          </Text>
        ))}
      </>
    )
  );
}

const styles = StyleSheet.create({
  barcodeTextURL: {
    fontSize: 20,
    color: 'white',
    fontWeight: 'bold',
  },
});
```

## Contributing

See the [contributing guide](CONTRIBUTING.md) to learn how to contribute to the repository and the development workflow.

## License

MIT
