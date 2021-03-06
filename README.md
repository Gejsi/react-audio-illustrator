<h1 align="center">Audio Illustrator</h1>
<h4 align="center">
  Visualize audio easily by creating highly customizable illustrators.
</h4>

## Install

```bash
npm install audio-illustrator
```

## Documentation

This package provides a simple class that helps receiving data from an audio, which can be used to create illustrators. <br />
There are a lot of packages that give pre-made components which aren't very customizable, because you can only modify them by changing some attributes but you can't create a new one.

```js
import Illustrator from 'audio-illustrator'

const illustrator = new Illustrator()

illustrator.connect(document.querySelector('#myAudio'))

const startDrawing = () => {
  illustrator.startLoop(startDrawing)
  // Get data for 18 items
  const audioData = illustrator.getData(18)
  // draw on canvas...
}

const stopDrawing = () => illustrator.stopLoop()
```

#### `Illustrator({ waveform: boolean })`

Contains all the methods. If `waveform` is true you get the current time-domain data, useful for creating waveform visualizations. <br />
Otherwise you get the current frequency data, useful for creating visualizations with bars.

#### `illustrator.connect(audio: HTMLAudioElement | HTMLVideoElement)`

Creates the objects which store the data.

#### `illustrator.disconnect()`

Dismantles the objects which store the data.

#### `illustrator.getData(items?: number)`

Provides real-time frequency or time-domain analysis information (depending on the
[waveform parameter](#illustrator-waveform-boolean-)) for the amount of items you need (default is 128).

#### `illustrator.startLoop(callback: FrameRequestCallback)`

Starts the loop using `requestAnimationFrame()`.

#### `illustrator.stopLoop()`

Stops the loop using `cancelAnimationFrame()`.

#### `illustrator.audioSrc`

Represents the audio source.

#### `illustrator.analyser`

Represents the [AnalyserNode](https://developer.mozilla.org/en-US/docs/Web/API/AnalyserNode)

## Usage with React

```jsx
import React, { useState, useEffect, useRef } from 'react'
import Illustrator from 'audio-illustrator'
import song from '...'
import Canvas from '...'

const App = () => {
  const [data, setData] = useState(new Uint8Array(0))
  const audioEl = useRef(null)
  let illustrator = useRef(null)

  useEffect(() => {
    illustrator.current = new Illustrator({ waveform: true })
    illustrator.current.connect(audioEl.current)

    return () => illustrator.current.disconnect()
  }, [])

  const handlePlay = () => {
    illustrator.current.analyser.context.resume().then(() => {
      illustrator.current.startLoop(handlePlay)
      setData(illustrator.current.getData())
    })
  }

  const handlePause = () => {
    illustrator.current.stopLoop()
  }

  return (
    <div>
      <audio
        ref={audioEl}
        onPlay={handlePlay}
        onPause={handlePause}
        src={song}
      ></audio>

      <Canvas data={data} />
    </div>
  )
}
```
