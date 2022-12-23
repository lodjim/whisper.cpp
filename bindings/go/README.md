# Go bindings for Whisper

This package provides Go bindings for whisper.cpp. They have been tested on:

  * Darwin (OS X) 12.6 on x64_64
  * Debian Linux on arm64
  * Fedora Linux on x86_64

The "low level" bindings are in the `bindings/go` directory and there is a more
Go-style package in the `bindings/go/pkg/whisper` directory. The most simple usage
is as follows:

```go
import (
	"github.com/ggerganov/whisper.cpp/bindings/go/pkg/whisper"
)

func main() {
	var modelpath string // Path to the model
	var samples []float32 // Samples to process

	// Load the model
	model, err := whisper.New(modelpath)
	if err != nil {
		panic(err)
	}
	defer model.Close()

	// Process samples
	context, err := model.NewContext()
	if err != nil {
		panic(err)
	}
	if err := context.Process(samples, nil); err != nil {
		return err
	}

	// Print out the results
	for {
		segment, err := context.NextSegment()
		if err != nil {
			break
		}
		fmt.Printf("[%6s->%6s] %s\n", segment.Start, segment.End, segment.Text)
	}
}
```

## Building & Testing

In order to build, you need to have the Go compiler installed. You can get it from [here](https://golang.org/dl/). Run the tests with:

```bash
git clone https://github.com/ggerganov/whisper.cpp.git
cd whisper.cpp/bindings/go
make test
```

This will compile a static `libwhisper.a` in a `build` folder, download a model file, then run the tests. To build the examples:

```bash
make examples
```

The examples are placed in the `build` directory. Once built, you can download all the models with the following command:

```bash
./build/go-model-download -out models
```

And you can then test a model against samples with the following command:

```bash
./build/go-whisper -model models/ggml-tiny.en.bin samples/jfk.wav 
```

