---
layout: post
title: "Building a Small App in Go"
date: 2014-08-02
---

A few weeks back, as part of [Matt Might's resolutions for programmers](http://matt.might.net/articles/programmers-resolutions/) I set up a [cron](http://en.wikipedia.org/wiki/Cron) task to automatically update my Homebrew packages on my Mac. 

It only took me about 5 minutes to figure out how cron worked and set it up: it's just  `0 */6 * * * /usr/local/bin/brew update`. I've felt like tweaking it, though, for a few reasons:

1. With a simple cron job, I get notifications in my terminal every time it runs. That's a little annoying. I don't really care about successful updates, but I'd like to know if something unexpected went wrong.
2. I'd still like to log the package updates in case I need that info later on.
3. I want to design and build a project from the ground up that I can really "own" for myself - you don't get that too frequently at work. 

I'm making it in [Go](http://golang.org/). I don't actually know Go all that well, but this is a pretty simple project that still handles a few important things across programming languages (file/network I/O, basic system interop). It could be a good Rosetta Stone sort of project that I can translate into different languages when I'm learning them.

I've heard a lot of people rave about Go, and it's picking up tons of use, so knowing it to a decent level seems like a good idea. 

Here's a general sketch of how my program will work:

1. First, it runs `brew update`, and gets the result. 
2. If Homebrew ran successfully, log it to a success log file and exit quietly.
3. If there was an issue updating Homebrew, check whether there's any internet access. If not, log that to a success log file and exit quietly.
4. If I have network access but there was some other problem updating, then report that to an exception log file. Store the current result/message in `/var/mail/eric` too.

I already know basic Go syntax, so I didn't need to do any reading on that. A bit of googling showed me the [`os/exec`](http://golang.org/pkg/os/exec/) package, for running external shell commands.

Here's a super-basic Go program that runs `brew update`. It does absolutely nothing else.

{% highlight go %}
// first version:
// just runs "brew update", waits for result, and exits

package main

import (
        "os/exec"
)
    
func main() {
        cmd := exec.Command("brew", "update")
        cmd.Run()
}
{% endhighlight %}

I'm not going to be making this a tutorial on Go, the [official website](http://golang.org/) already has a good one.

This is the simplest program we can make for this purpose. I didn't import `fmt`, so I can't even print anything to the terminal. The `Command` object doesn't even have its stdin/out/err redirected. All this program does is silently run `brew update` and exit.

The next step is capturing the output of the command. Version 2 creates buffers to store the command's stdout and stderr, and prints them once the command finishes running. 
Here's version 2:

{% highlight go %}
package main

import (
    	"bytes"
    	"fmt"
    	"os/exec"
)

func main() {
    	cmd := exec.Command("brew", "update")
    	var out bytes.Buffer
    	var err bytes.Buffer
    	cmd.Stdout = &out
    	cmd.Stderr = &err
    	fmt.Println("Running brew update now...")
    	cmd.Run()
    	fmt.Printf("Stdout from brew update: %q\n", out.String())
    	fmt.Printf("Stderr from brew update: %q\n", err.String())
}
{% endhighlight %}

We're just creating some buffers for `cmd` to return its output to, and printing them once the command has run.

When we run this program, we get some helpful output:

    erics-mbp% go run cron-ex2.go
    Running brew update now...
    Stdout from brew update: "Already up to date.\n"
    Stderr from brew update: ""

This is way better than version 1, because we actually see the results of our command! It's not all the way there yet, though. Remember, we want to silently record successful updates. The standard library's [`log`](http://golang.org/pkg/log) isn't really suited for this - it'd be better for logging things like requests sent to a web server.

So I'm just writing to a plain text file. I didn't find a function for opening a file for writing *or* appending (if it already exists) in the standard library, so I wrote up my own:

{% highlight go %}
func AppendFile(name string) (\*os.File, error) {
    	if _, err := os.Stat(name); err != nil {
    		if os.IsNotExist(err) {
    			return os.Create(name)
    		}
    	}
    	return os.OpenFile(name, os.O_APPEND | os.O_RDWR, 0660)
}
{% endhighlight %}

Again, I'm not writing a tutorial here, so I'll just breeze over the details. I try to get the file's info with `os.Stat`, and if that fails because the file doesn't exist, I make a new file with that name. If it succeeds, I open it in read-write mode, appending anything it writes to the end of the file.

I kind of like this approach. It hides less from the programmer than the standard open functions in C# and Python. I've heard that Go was designed to be a simple language that doesn't hide much from the programmer, like C, and I can see that here. 

It took a few minutes to write, but if I write any serious Go programs, `AppendFile` is probably going into a helper function library.

While we're improving this program, we can time how long it takes to run using [`time`](http://golang.org/pkg/time/). There's a simple example in the documentation, under [`time.Duration`](http://golang.org/pkg/time/#Duration). I used `time.Since`, just below the `Duration` example, but they work identically.

Here's the updated `main` function:

{% highlight go %}
func main() {
	cmd := exec.Command("brew", "update")
	var bufout bytes.Buffer
	var buferr bytes.Buffer

	home := os.Getenv("HOME")
	logPath := path.Join(home, LOG_FILE_NAME)
	logFile, err := AppendFile(logPath)

	if err != nil {
		log.Fatal(err)
	}
	defer logFile.Close()

	cmd.Stdout = &bufout
	cmd.Stderr = &buferr
	start := time.Now()
	cmd.Run()

	elapsed := time.Since(start)
	logFile.WriteString(start.String())
	logFile.WriteString("\nTime needed to run: ")
	logFile.WriteString(elapsed.String())
	logFile.WriteString("\nStdout from brew update: ")
	logFile.WriteString(bufout.String())
	logFile.WriteString("\nStderr from brew update: ")
	logFile.WriteString(buferr.String())
	logFile.WriteString("\n\n")
}
{% endhighlight %}

`defer` is a pretty nice example of how *easy* Go makes asynchronous programming. What it does is add the function call onto a list of calls to run once the current function finishes. In bigger, more complex functions, `defer` would help keep code clean - you wouldn't have to add an `f.Close()` to the end of every branch of a conditional, for example. 

[Here's some more info on `defer`.](http://blog.golang.org/defer-panic-and-recover)

For my final version, I decided to add a tiny bit of real concurrency to this program.As the `brew update` command runs, I start up a separate process that tries to ping Google to check whether I've got network access.

Here's the function that checks if we can access Google:

{% highlight go %}
func testConn(status chan error) {
    	conn, err := net.Dial("tcp", TEST_CONN)
    	status <- err
    	if err == nil {
    		conn.Close()
    	}
}
{% endhighlight %} 

Channels are a new concept to me! That might be a little embarrassing, but I know I'm not the only one, so let's go over what they are.

A *channel* is an object that lets you send data between different functions. That's all it is. You can send one function off to run asynchronously -- independently -- of the caller, do some more work, and then wait for the other function to report back. That's what the channel does. 

In `testConn`, we try to connect to Google. Most Go functions that can possibly "fail" return two values: the thing they should return on success, and an error object. If the function ran successfully, the error value is `nil`. Right here we're using that to make sure that the connection gets closed if we succeed in connecting. The `status` message gets passed back to the caller by way of the `chan` argument testConn takes.

{% highlight go %}
func main() {
	cmd := exec.Command("brew", "update")
	var bufout bytes.Buffer
	var buferr bytes.Buffer

	home := os.Getenv("HOME")
	logPath := path.Join(home, LOG_FILE_NAME)
	logFile, err := AppendFile(logPath)

	if err != nil {
		log.Fatal(err)
	}
	defer logFile.Close()

	status := make(chan error)
	go testConn(status)

	cmd.Stdout = &bufout
	cmd.Stderr = &buferr
	start := time.Now()
	cmd.Run()

	elapsed := time.Since(start)	
	timeTaken := fmt.Sprintf("\nTime needed to run: %v\n", elapsed.String())
	stdOut := fmt.Sprintf("Stdout from brew update: %v\n", bufout.String())
	stdErr := fmt.Sprintf("Stderr from brew update: %v\n", buferr.String())

	netStat := <-status
	if netStat != nil {
		errMsg := fmt.Sprintf("Network error encountered: %v\n", netStat.Error())
		log.Print(errMsg)
		logFile.WriteString(errMsg)
	}

	logFile.WriteString(start.String())
	logFile.WriteString(timeTaken)
	logFile.WriteString(stdOut)
	logFile.WriteString(stdErr)
	logFile.WriteString("\n")
}
{% endhighlight %}

Here's `main`, and how it handles the other end of the channel. Channels, maps (aka dictionaries), slices (arbitrary-length arrays) and *n*-element arrays can all be created using Go's `make` function. We're setting the channel up here, and after that we have it run asynchronously, in its own goroutine, using the `go` keyword. `main` keeps running after that. 

Once we hit this line:

`netStat := <-status`

`main` halts and waits to receive something back from the channel we gave to `testConn`. If it returns an actual error message, there's a network issue and we log that. The network error gets sent to both stdout (which cron packages up and adds to a mail file to notify me later) and my own logging file. Then we write out the time it took to run along with the output from the command to another log file.

Here's a gist containing the full source for all four of the examples: https://gist.github.com/EFulmer/bcde405b38442bd74c2c

This was fun to write, and I learned a bit about Go and concurrency. I'm not sure if I'm entirely sold on Go, but I can see why people would use it. The fast compile times let you get something like an interactive REPL, it's a lot more lightweight and easy to read/write than Java, and it's apparently pretty fast. 

Now back to finishing my music recommendation Flask app and writing an interpreter or compiler!
