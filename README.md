
## OpenAI Voice Utils

This tools are extracted from [openai-realtime-console repository](https://github.com/openai/openai-realtime-console/tree/main/src/lib/wavtools) and it is mainly used to record audio, send it to a real-time API, and play back the audio it returns.

### Brief tutorial
This tutorial has been extracted from the repository as well.

Wavtools contains easy management of PCM16 audio streams in the browser, both recording and playing.

#### WavRecorder Quickstart
    import { WavRecorder } from '/src/lib/wavtools/index.js';

    const wavRecorder = new WavRecorder({ sampleRate: 24000 });
    wavRecorder.getStatus(); // "ended"

    // request permissions, connect microphone
    await wavRecorder.begin();
    wavRecorder.getStatus(); // "paused"

    // Start recording
    // This callback will be triggered in chunks of 8192 samples by default
    // { mono, raw } are Int16Array (PCM16) mono & full channel data
    await wavRecorder.record((data) => {
      const { mono, raw } = data;
    });
    wavRecorder.getStatus(); // "recording"

    // Stop recording
    await wavRecorder.pause();
    wavRecorder.getStatus(); // "paused"

    // outputs "audio/wav" audio file
    const audio = await wavRecorder.save();

    // clears current audio buffer and starts recording
    await wavRecorder.clear();
    await wavRecorder.record();

    // get data for visualization
    const frequencyData = wavRecorder.getFrequencies();

    // Stop recording, disconnects microphone, output file
    await wavRecorder.pause();
    const finalAudio = await wavRecorder.end();

    // Listen for device change; e.g. if somebody disconnects a microphone
    // deviceList is array of MediaDeviceInfo[] + `default` property
    wavRecorder.listenForDeviceChange((deviceList) => {});

#### WavStreamPlayer Quickstart

    import { WavStreamPlayer } from '/src/lib/wavtools/index.js';

    const wavStreamPlayer = new WavStreamPlayer({ sampleRate: 24000 });

    // Connect to audio output
    await wavStreamPlayer.connect();

    // Create 1s of empty PCM16 audio
    const audio = new Int16Array(24000);
    // Queue 3s of audio, will start playing immediately
    wavStreamPlayer.add16BitPCM(audio, 'my-track');
    wavStreamPlayer.add16BitPCM(audio, 'my-track');
    wavStreamPlayer.add16BitPCM(audio, 'my-track');

    // get data for visualization
    const frequencyData = wavStreamPlayer.getFrequencies();

    // Interrupt the audio (halt playback) at any time
    // To restart, need to call .add16BitPCM() again
    const trackOffset = await wavStreamPlayer.interrupt();
    trackOffset.trackId; // "my-track"
    trackOffset.offset; // sample number
    trackOffset.currentTime; // time in track
