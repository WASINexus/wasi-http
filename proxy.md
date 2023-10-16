<h1><a name="proxy">World proxy</a></h1>
<ul>
<li>Imports:
<ul>
<li>interface <a href="#wasi:clocks_wall_clock"><code>wasi:clocks/wall-clock</code></a></li>
<li>interface <a href="#wasi:io_poll"><code>wasi:io/poll</code></a></li>
<li>interface <a href="#wasi:clocks_monotonic_clock"><code>wasi:clocks/monotonic-clock</code></a></li>
<li>interface <a href="#wasi:clocks_timezone"><code>wasi:clocks/timezone</code></a></li>
<li>interface <a href="#wasi:random_random"><code>wasi:random/random</code></a></li>
<li>interface <a href="#wasi:io_streams"><code>wasi:io/streams</code></a></li>
<li>interface <a href="#wasi:cli_stdout"><code>wasi:cli/stdout</code></a></li>
<li>interface <a href="#wasi:cli_stderr"><code>wasi:cli/stderr</code></a></li>
<li>interface <a href="#wasi:cli_stdin"><code>wasi:cli/stdin</code></a></li>
<li>interface <a href="#wasi:http_types"><code>wasi:http/types</code></a></li>
<li>interface <a href="#wasi:http_outgoing_handler"><code>wasi:http/outgoing-handler</code></a></li>
</ul>
</li>
<li>Exports:
<ul>
<li>interface <a href="#wasi:http_incoming_handler"><code>wasi:http/incoming-handler</code></a></li>
</ul>
</li>
</ul>
<h2><a name="wasi:clocks_wall_clock">Import interface wasi:clocks/wall-clock</a></h2>
<p>WASI Wall Clock is a clock API intended to let users query the current
time. The name &quot;wall&quot; makes an analogy to a &quot;clock on the wall&quot;, which
is not necessarily monotonic as it may be reset.</p>
<p>It is intended to be portable at least between Unix-family platforms and
Windows.</p>
<p>A wall clock is a clock which measures the date and time according to
some external reference.</p>
<p>External references may be reset, so this clock is not necessarily
monotonic, making it unsuitable for measuring elapsed time.</p>
<p>It is intended for reporting the current date and time for humans.</p>
<hr />
<h3>Types</h3>
<h4><a name="datetime"><code>record datetime</code></a></h4>
<p>A time and date in seconds plus nanoseconds.</p>
<h5>Record Fields</h5>
<ul>
<li><a name="datetime.seconds"><code>seconds</code></a>: <code>u64</code></li>
<li><a name="datetime.nanoseconds"><code>nanoseconds</code></a>: <code>u32</code></li>
</ul>
<hr />
<h3>Functions</h3>
<h4><a name="now"><code>now: func</code></a></h4>
<p>Read the current value of the clock.</p>
<p>This clock is not monotonic, therefore calling this function repeatedly
will not necessarily produce a sequence of non-decreasing values.</p>
<p>The returned timestamps represent the number of seconds since
1970-01-01T00:00:00Z, also known as <a href="https://pubs.opengroup.org/onlinepubs/9699919799/xrat/V4_xbd_chap04.html#tag_21_04_16">POSIX's Seconds Since the Epoch</a>,
also known as <a href="https://en.wikipedia.org/wiki/Unix_time">Unix Time</a>.</p>
<p>The nanoseconds field of the output is always less than 1000000000.</p>
<h5>Return values</h5>
<ul>
<li><a name="now.0"></a> <a href="#datetime"><a href="#datetime"><code>datetime</code></a></a></li>
</ul>
<h4><a name="resolution"><code>resolution: func</code></a></h4>
<p>Query the resolution of the clock.</p>
<p>The nanoseconds field of the output is always less than 1000000000.</p>
<h5>Return values</h5>
<ul>
<li><a name="resolution.0"></a> <a href="#datetime"><a href="#datetime"><code>datetime</code></a></a></li>
</ul>
<h2><a name="wasi:io_poll">Import interface wasi:io/poll</a></h2>
<p>A poll API intended to let users wait for I/O events on multiple handles
at once.</p>
<hr />
<h3>Types</h3>
<h4><a name="pollable"><code>resource pollable</code></a></h4>
<hr />
<h3>Functions</h3>
<h4><a name="poll_list"><code>poll-list: func</code></a></h4>
<p>Poll for completion on a set of pollables.</p>
<p>This function takes a list of pollables, which identify I/O sources of
interest, and waits until one or more of the events is ready for I/O.</p>
<p>The result <code>list&lt;u32&gt;</code> contains one or more indices of handles in the
argument list that is ready for I/O.</p>
<p>If the list contains more elements than can be indexed with a <code>u32</code>
value, this function traps.</p>
<p>A timeout can be implemented by adding a pollable from the
wasi-clocks API to the list.</p>
<p>This function does not return a <code>result</code>; polling in itself does not
do any I/O so it doesn't fail. If any of the I/O sources identified by
the pollables has an error, it is indicated by marking the source as
being reaedy for I/O.</p>
<h5>Params</h5>
<ul>
<li><a name="poll_list.in"><code>in</code></a>: list&lt;borrow&lt;<a href="#pollable"><a href="#pollable"><code>pollable</code></a></a>&gt;&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="poll_list.0"></a> list&lt;<code>u32</code>&gt;</li>
</ul>
<h4><a name="poll_one"><code>poll-one: func</code></a></h4>
<p>Poll for completion on a single pollable.</p>
<p>This function is similar to <a href="#poll_list"><code>poll-list</code></a>, but operates on only a single
pollable. When it returns, the handle is ready for I/O.</p>
<h5>Params</h5>
<ul>
<li><a name="poll_one.in"><code>in</code></a>: borrow&lt;<a href="#pollable"><a href="#pollable"><code>pollable</code></a></a>&gt;</li>
</ul>
<h2><a name="wasi:clocks_monotonic_clock">Import interface wasi:clocks/monotonic-clock</a></h2>
<p>WASI Monotonic Clock is a clock API intended to let users measure elapsed
time.</p>
<p>It is intended to be portable at least between Unix-family platforms and
Windows.</p>
<p>A monotonic clock is a clock which has an unspecified initial value, and
successive reads of the clock will produce non-decreasing values.</p>
<p>It is intended for measuring elapsed time.</p>
<hr />
<h3>Types</h3>
<h4><a name="pollable"><code>type pollable</code></a></h4>
<p><a href="#pollable"><a href="#pollable"><code>pollable</code></a></a></p>
<p>
#### <a name="instant">`type instant`</a>
`u64`
<p>A timestamp in nanoseconds.
<hr />
<h3>Functions</h3>
<h4><a name="now"><code>now: func</code></a></h4>
<p>Read the current value of the clock.</p>
<p>The clock is monotonic, therefore calling this function repeatedly will
produce a sequence of non-decreasing values.</p>
<h5>Return values</h5>
<ul>
<li><a name="now.0"></a> <a href="#instant"><a href="#instant"><code>instant</code></a></a></li>
</ul>
<h4><a name="resolution"><code>resolution: func</code></a></h4>
<p>Query the resolution of the clock.</p>
<h5>Return values</h5>
<ul>
<li><a name="resolution.0"></a> <a href="#instant"><a href="#instant"><code>instant</code></a></a></li>
</ul>
<h4><a name="subscribe"><code>subscribe: func</code></a></h4>
<p>Create a <a href="#pollable"><code>pollable</code></a> which will resolve once the specified time has been
reached.</p>
<h5>Params</h5>
<ul>
<li><a name="subscribe.when"><code>when</code></a>: <a href="#instant"><a href="#instant"><code>instant</code></a></a></li>
<li><a name="subscribe.absolute"><code>absolute</code></a>: <code>bool</code></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="subscribe.0"></a> own&lt;<a href="#pollable"><a href="#pollable"><code>pollable</code></a></a>&gt;</li>
</ul>
<h2><a name="wasi:clocks_timezone">Import interface wasi:clocks/timezone</a></h2>
<hr />
<h3>Types</h3>
<h4><a name="datetime"><code>type datetime</code></a></h4>
<p><a href="#datetime"><a href="#datetime"><code>datetime</code></a></a></p>
<p>
#### <a name="timezone_display">`record timezone-display`</a>
<p>Information useful for displaying the timezone of a specific <a href="#datetime"><code>datetime</code></a>.</p>
<p>This information may vary within a single <code>timezone</code> to reflect daylight
saving time adjustments.</p>
<h5>Record Fields</h5>
<ul>
<li>
<p><a name="timezone_display.utc_offset"><a href="#utc_offset"><code>utc-offset</code></a></a>: <code>s32</code></p>
<p>The number of seconds difference between UTC time and the local
time of the timezone.
<p>The returned value will always be less than 86400 which is the
number of seconds in a day (24<em>60</em>60).</p>
<p>In implementations that do not expose an actual time zone, this
should return 0.</p>
</li>
<li>
<p><a name="timezone_display.name"><code>name</code></a>: <code>string</code></p>
<p>The abbreviated name of the timezone to display to a user. The name
`UTC` indicates Coordinated Universal Time. Otherwise, this should
reference local standards for the name of the time zone.
<p>In implementations that do not expose an actual time zone, this
should be the string <code>UTC</code>.</p>
<p>In time zones that do not have an applicable name, a formatted
representation of the UTC offset may be returned, such as <code>-04:00</code>.</p>
</li>
<li>
<p><a name="timezone_display.in_daylight_saving_time"><code>in-daylight-saving-time</code></a>: <code>bool</code></p>
<p>Whether daylight saving time is active.
<p>In implementations that do not expose an actual time zone, this
should return false.</p>
</li>
</ul>
<hr />
<h3>Functions</h3>
<h4><a name="display"><code>display: func</code></a></h4>
<p>Return information needed to display the given <a href="#datetime"><code>datetime</code></a>. This includes
the UTC offset, the time zone name, and a flag indicating whether
daylight saving time is active.</p>
<p>If the timezone cannot be determined for the given <a href="#datetime"><code>datetime</code></a>, return a
<a href="#timezone_display"><code>timezone-display</code></a> for <code>UTC</code> with a <a href="#utc_offset"><code>utc-offset</code></a> of 0 and no daylight
saving time.</p>
<h5>Params</h5>
<ul>
<li><a name="display.when"><code>when</code></a>: <a href="#datetime"><a href="#datetime"><code>datetime</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="display.0"></a> <a href="#timezone_display"><a href="#timezone_display"><code>timezone-display</code></a></a></li>
</ul>
<h4><a name="utc_offset"><code>utc-offset: func</code></a></h4>
<p>The same as <a href="#display"><code>display</code></a>, but only return the UTC offset.</p>
<h5>Params</h5>
<ul>
<li><a name="utc_offset.when"><code>when</code></a>: <a href="#datetime"><a href="#datetime"><code>datetime</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="utc_offset.0"></a> <code>s32</code></li>
</ul>
<h2><a name="wasi:random_random">Import interface wasi:random/random</a></h2>
<p>WASI Random is a random data API.</p>
<p>It is intended to be portable at least between Unix-family platforms and
Windows.</p>
<hr />
<h3>Functions</h3>
<h4><a name="get_random_bytes"><code>get-random-bytes: func</code></a></h4>
<p>Return <code>len</code> cryptographically-secure random or pseudo-random bytes.</p>
<p>This function must produce data at least as cryptographically secure and
fast as an adequately seeded cryptographically-secure pseudo-random
number generator (CSPRNG). It must not block, from the perspective of
the calling program, under any circumstances, including on the first
request and on requests for numbers of bytes. The returned data must
always be unpredictable.</p>
<p>This function must always return fresh data. Deterministic environments
must omit this function, rather than implementing it with deterministic
data.</p>
<h5>Params</h5>
<ul>
<li><a name="get_random_bytes.len"><code>len</code></a>: <code>u64</code></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="get_random_bytes.0"></a> list&lt;<code>u8</code>&gt;</li>
</ul>
<h4><a name="get_random_u64"><code>get-random-u64: func</code></a></h4>
<p>Return a cryptographically-secure random or pseudo-random <code>u64</code> value.</p>
<p>This function returns the same type of data as <a href="#get_random_bytes"><code>get-random-bytes</code></a>,
represented as a <code>u64</code>.</p>
<h5>Return values</h5>
<ul>
<li><a name="get_random_u64.0"></a> <code>u64</code></li>
</ul>
<h2><a name="wasi:io_streams">Import interface wasi:io/streams</a></h2>
<p>WASI I/O is an I/O abstraction API which is currently focused on providing
stream types.</p>
<p>In the future, the component model is expected to add built-in stream types;
when it does, they are expected to subsume this API.</p>
<hr />
<h3>Types</h3>
<h4><a name="pollable"><code>type pollable</code></a></h4>
<p><a href="#pollable"><a href="#pollable"><code>pollable</code></a></a></p>
<p>
#### <a name="stream_status">`enum stream-status`</a>
<p>Streams provide a sequence of data and then end; once they end, they
no longer provide any further data.</p>
<p>For example, a stream reading from a file ends when the stream reaches
the end of the file. For another example, a stream reading from a
socket ends when the socket is closed.</p>
<h5>Enum Cases</h5>
<ul>
<li>
<p><a name="stream_status.open"><code>open</code></a></p>
<p>The stream is open and may produce further data.
</li>
<li>
<p><a name="stream_status.ended"><code>ended</code></a></p>
<p>When reading, this indicates that the stream will not produce
further data.
When writing, this indicates that the stream will no longer be read.
Further writes are still permitted.
</li>
</ul>
<h4><a name="input_stream"><code>resource input-stream</code></a></h4>
<h4><a name="write_error"><code>enum write-error</code></a></h4>
<p>An error for output-stream operations.</p>
<p>Contrary to input-streams, a closed output-stream is reported using
an error.</p>
<h5>Enum Cases</h5>
<ul>
<li>
<p><a name="write_error.last_operation_failed"><code>last-operation-failed</code></a></p>
<p>The last operation (a write or flush) failed before completion.
</li>
<li>
<p><a name="write_error.closed"><code>closed</code></a></p>
<p>The stream is closed: no more input will be accepted by the
stream. A closed output-stream will return this error on all
future operations.
</li>
</ul>
<h4><a name="output_stream"><code>resource output-stream</code></a></h4>
<hr />
<h3>Functions</h3>
<h4><a name="method_input_stream.read"><code>[method]input-stream.read: func</code></a></h4>
<p>Perform a non-blocking read from the stream.</p>
<p>This function returns a list of bytes containing the data that was
read, along with a <a href="#stream_status"><code>stream-status</code></a> which, indicates whether further
reads are expected to produce data. The returned list will contain up to
<code>len</code> bytes; it may return fewer than requested, but not more. An
empty list and <code>stream-status:open</code> indicates no more data is
available at this time, and that the pollable given by <a href="#subscribe"><code>subscribe</code></a>
will be ready when more data is available.</p>
<p>Once a stream has reached the end, subsequent calls to <code>read</code> or
<code>skip</code> will always report <code>stream-status:ended</code> rather than producing more
data.</p>
<p>When the caller gives a <code>len</code> of 0, it represents a request to read 0
bytes. This read should  always succeed and return an empty list and
the current <a href="#stream_status"><code>stream-status</code></a>.</p>
<p>The <code>len</code> parameter is a <code>u64</code>, which could represent a list of u8 which
is not possible to allocate in wasm32, or not desirable to allocate as
as a return value by the callee. The callee may return a list of bytes
less than <code>len</code> in size while more bytes are available for reading.</p>
<h5>Params</h5>
<ul>
<li><a name="method_input_stream.read.self"><code>self</code></a>: borrow&lt;<a href="#input_stream"><a href="#input_stream"><code>input-stream</code></a></a>&gt;</li>
<li><a name="method_input_stream.read.len"><code>len</code></a>: <code>u64</code></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_input_stream.read.0"></a> result&lt;(list&lt;<code>u8</code>&gt;, <a href="#stream_status"><a href="#stream_status"><code>stream-status</code></a></a>)&gt;</li>
</ul>
<h4><a name="method_input_stream.blocking_read"><code>[method]input-stream.blocking-read: func</code></a></h4>
<p>Read bytes from a stream, after blocking until at least one byte can
be read. Except for blocking, identical to <code>read</code>.</p>
<h5>Params</h5>
<ul>
<li><a name="method_input_stream.blocking_read.self"><code>self</code></a>: borrow&lt;<a href="#input_stream"><a href="#input_stream"><code>input-stream</code></a></a>&gt;</li>
<li><a name="method_input_stream.blocking_read.len"><code>len</code></a>: <code>u64</code></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_input_stream.blocking_read.0"></a> result&lt;(list&lt;<code>u8</code>&gt;, <a href="#stream_status"><a href="#stream_status"><code>stream-status</code></a></a>)&gt;</li>
</ul>
<h4><a name="method_input_stream.skip"><code>[method]input-stream.skip: func</code></a></h4>
<p>Skip bytes from a stream.</p>
<p>This is similar to the <code>read</code> function, but avoids copying the
bytes into the instance.</p>
<p>Once a stream has reached the end, subsequent calls to read or
<code>skip</code> will always report end-of-stream rather than producing more
data.</p>
<p>This function returns the number of bytes skipped, along with a
<a href="#stream_status"><code>stream-status</code></a> indicating whether the end of the stream was
reached. The returned value will be at most <code>len</code>; it may be less.</p>
<h5>Params</h5>
<ul>
<li><a name="method_input_stream.skip.self"><code>self</code></a>: borrow&lt;<a href="#input_stream"><a href="#input_stream"><code>input-stream</code></a></a>&gt;</li>
<li><a name="method_input_stream.skip.len"><code>len</code></a>: <code>u64</code></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_input_stream.skip.0"></a> result&lt;(<code>u64</code>, <a href="#stream_status"><a href="#stream_status"><code>stream-status</code></a></a>)&gt;</li>
</ul>
<h4><a name="method_input_stream.blocking_skip"><code>[method]input-stream.blocking-skip: func</code></a></h4>
<p>Skip bytes from a stream, after blocking until at least one byte
can be skipped. Except for blocking behavior, identical to <code>skip</code>.</p>
<h5>Params</h5>
<ul>
<li><a name="method_input_stream.blocking_skip.self"><code>self</code></a>: borrow&lt;<a href="#input_stream"><a href="#input_stream"><code>input-stream</code></a></a>&gt;</li>
<li><a name="method_input_stream.blocking_skip.len"><code>len</code></a>: <code>u64</code></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_input_stream.blocking_skip.0"></a> result&lt;(<code>u64</code>, <a href="#stream_status"><a href="#stream_status"><code>stream-status</code></a></a>)&gt;</li>
</ul>
<h4><a name="method_input_stream.subscribe"><code>[method]input-stream.subscribe: func</code></a></h4>
<p>Create a <a href="#pollable"><code>pollable</code></a> which will resolve once either the specified stream
has bytes available to read or the other end of the stream has been
closed.
The created <a href="#pollable"><code>pollable</code></a> is a child resource of the <a href="#input_stream"><code>input-stream</code></a>.
Implementations may trap if the <a href="#input_stream"><code>input-stream</code></a> is dropped before
all derived <a href="#pollable"><code>pollable</code></a>s created with this function are dropped.</p>
<h5>Params</h5>
<ul>
<li><a name="method_input_stream.subscribe.self"><code>self</code></a>: borrow&lt;<a href="#input_stream"><a href="#input_stream"><code>input-stream</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_input_stream.subscribe.0"></a> own&lt;<a href="#pollable"><a href="#pollable"><code>pollable</code></a></a>&gt;</li>
</ul>
<h4><a name="method_output_stream.check_write"><code>[method]output-stream.check-write: func</code></a></h4>
<p>Check readiness for writing. This function never blocks.</p>
<p>Returns the number of bytes permitted for the next call to <code>write</code>,
or an error. Calling <code>write</code> with more bytes than this function has
permitted will trap.</p>
<p>When this function returns 0 bytes, the <a href="#subscribe"><code>subscribe</code></a> pollable will
become ready when this function will report at least 1 byte, or an
error.</p>
<h5>Params</h5>
<ul>
<li><a name="method_output_stream.check_write.self"><code>self</code></a>: borrow&lt;<a href="#output_stream"><a href="#output_stream"><code>output-stream</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_output_stream.check_write.0"></a> result&lt;<code>u64</code>, <a href="#write_error"><a href="#write_error"><code>write-error</code></a></a>&gt;</li>
</ul>
<h4><a name="method_output_stream.write"><code>[method]output-stream.write: func</code></a></h4>
<p>Perform a write. This function never blocks.</p>
<p>Precondition: check-write gave permit of Ok(n) and contents has a
length of less than or equal to n. Otherwise, this function will trap.</p>
<p>returns Err(closed) without writing if the stream has closed since
the last call to check-write provided a permit.</p>
<h5>Params</h5>
<ul>
<li><a name="method_output_stream.write.self"><code>self</code></a>: borrow&lt;<a href="#output_stream"><a href="#output_stream"><code>output-stream</code></a></a>&gt;</li>
<li><a name="method_output_stream.write.contents"><code>contents</code></a>: list&lt;<code>u8</code>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_output_stream.write.0"></a> result&lt;_, <a href="#write_error"><a href="#write_error"><code>write-error</code></a></a>&gt;</li>
</ul>
<h4><a name="method_output_stream.blocking_write_and_flush"><code>[method]output-stream.blocking-write-and-flush: func</code></a></h4>
<p>Perform a write of up to 4096 bytes, and then flush the stream. Block
until all of these operations are complete, or an error occurs.</p>
<p>This is a convenience wrapper around the use of <code>check-write</code>,
<a href="#subscribe"><code>subscribe</code></a>, <code>write</code>, and <code>flush</code>, and is implemented with the
following pseudo-code:</p>
<pre><code class="language-text">let pollable = this.subscribe();
while !contents.is_empty() {
  // Wait for the stream to become writable
  poll-one(pollable);
  let Ok(n) = this.check-write(); // eliding error handling
  let len = min(n, contents.len());
  let (chunk, rest) = contents.split_at(len);
  this.write(chunk  );            // eliding error handling
  contents = rest;
}
this.flush();
// Wait for completion of `flush`
poll-one(pollable);
// Check for any errors that arose during `flush`
let _ = this.check-write();         // eliding error handling
</code></pre>
<h5>Params</h5>
<ul>
<li><a name="method_output_stream.blocking_write_and_flush.self"><code>self</code></a>: borrow&lt;<a href="#output_stream"><a href="#output_stream"><code>output-stream</code></a></a>&gt;</li>
<li><a name="method_output_stream.blocking_write_and_flush.contents"><code>contents</code></a>: list&lt;<code>u8</code>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_output_stream.blocking_write_and_flush.0"></a> result&lt;_, <a href="#write_error"><a href="#write_error"><code>write-error</code></a></a>&gt;</li>
</ul>
<h4><a name="method_output_stream.flush"><code>[method]output-stream.flush: func</code></a></h4>
<p>Request to flush buffered output. This function never blocks.</p>
<p>This tells the output-stream that the caller intends any buffered
output to be flushed. the output which is expected to be flushed
is all that has been passed to <code>write</code> prior to this call.</p>
<p>Upon calling this function, the <a href="#output_stream"><code>output-stream</code></a> will not accept any
writes (<code>check-write</code> will return <code>ok(0)</code>) until the flush has
completed. The <a href="#subscribe"><code>subscribe</code></a> pollable will become ready when the
flush has completed and the stream can accept more writes.</p>
<h5>Params</h5>
<ul>
<li><a name="method_output_stream.flush.self"><code>self</code></a>: borrow&lt;<a href="#output_stream"><a href="#output_stream"><code>output-stream</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_output_stream.flush.0"></a> result&lt;_, <a href="#write_error"><a href="#write_error"><code>write-error</code></a></a>&gt;</li>
</ul>
<h4><a name="method_output_stream.blocking_flush"><code>[method]output-stream.blocking-flush: func</code></a></h4>
<p>Request to flush buffered output, and block until flush completes
and stream is ready for writing again.</p>
<h5>Params</h5>
<ul>
<li><a name="method_output_stream.blocking_flush.self"><code>self</code></a>: borrow&lt;<a href="#output_stream"><a href="#output_stream"><code>output-stream</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_output_stream.blocking_flush.0"></a> result&lt;_, <a href="#write_error"><a href="#write_error"><code>write-error</code></a></a>&gt;</li>
</ul>
<h4><a name="method_output_stream.subscribe"><code>[method]output-stream.subscribe: func</code></a></h4>
<p>Create a <a href="#pollable"><code>pollable</code></a> which will resolve once the output-stream
is ready for more writing, or an error has occured. When this
pollable is ready, <code>check-write</code> will return <code>ok(n)</code> with n&gt;0, or an
error.</p>
<p>If the stream is closed, this pollable is always ready immediately.</p>
<p>The created <a href="#pollable"><code>pollable</code></a> is a child resource of the <a href="#output_stream"><code>output-stream</code></a>.
Implementations may trap if the <a href="#output_stream"><code>output-stream</code></a> is dropped before
all derived <a href="#pollable"><code>pollable</code></a>s created with this function are dropped.</p>
<h5>Params</h5>
<ul>
<li><a name="method_output_stream.subscribe.self"><code>self</code></a>: borrow&lt;<a href="#output_stream"><a href="#output_stream"><code>output-stream</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_output_stream.subscribe.0"></a> own&lt;<a href="#pollable"><a href="#pollable"><code>pollable</code></a></a>&gt;</li>
</ul>
<h4><a name="method_output_stream.write_zeroes"><code>[method]output-stream.write-zeroes: func</code></a></h4>
<p>Write zeroes to a stream.</p>
<p>this should be used precisely like <code>write</code> with the exact same
preconditions (must use check-write first), but instead of
passing a list of bytes, you simply pass the number of zero-bytes
that should be written.</p>
<h5>Params</h5>
<ul>
<li><a name="method_output_stream.write_zeroes.self"><code>self</code></a>: borrow&lt;<a href="#output_stream"><a href="#output_stream"><code>output-stream</code></a></a>&gt;</li>
<li><a name="method_output_stream.write_zeroes.len"><code>len</code></a>: <code>u64</code></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_output_stream.write_zeroes.0"></a> result&lt;_, <a href="#write_error"><a href="#write_error"><code>write-error</code></a></a>&gt;</li>
</ul>
<h4><a name="method_output_stream.blocking_write_zeroes_and_flush"><code>[method]output-stream.blocking-write-zeroes-and-flush: func</code></a></h4>
<p>Perform a write of up to 4096 zeroes, and then flush the stream.
Block until all of these operations are complete, or an error
occurs.</p>
<p>This is a convenience wrapper around the use of <code>check-write</code>,
<a href="#subscribe"><code>subscribe</code></a>, <code>write-zeroes</code>, and <code>flush</code>, and is implemented with
the following pseudo-code:</p>
<pre><code class="language-text">let pollable = this.subscribe();
while num_zeroes != 0 {
  // Wait for the stream to become writable
  poll-one(pollable);
  let Ok(n) = this.check-write(); // eliding error handling
  let len = min(n, num_zeroes);
  this.write-zeroes(len);         // eliding error handling
  num_zeroes -= len;
}
this.flush();
// Wait for completion of `flush`
poll-one(pollable);
// Check for any errors that arose during `flush`
let _ = this.check-write();         // eliding error handling
</code></pre>
<h5>Params</h5>
<ul>
<li><a name="method_output_stream.blocking_write_zeroes_and_flush.self"><code>self</code></a>: borrow&lt;<a href="#output_stream"><a href="#output_stream"><code>output-stream</code></a></a>&gt;</li>
<li><a name="method_output_stream.blocking_write_zeroes_and_flush.len"><code>len</code></a>: <code>u64</code></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_output_stream.blocking_write_zeroes_and_flush.0"></a> result&lt;_, <a href="#write_error"><a href="#write_error"><code>write-error</code></a></a>&gt;</li>
</ul>
<h4><a name="method_output_stream.splice"><code>[method]output-stream.splice: func</code></a></h4>
<p>Read from one stream and write to another.</p>
<p>This function returns the number of bytes transferred; it may be less
than <code>len</code>.</p>
<p>Unlike other I/O functions, this function blocks until all the data
read from the input stream has been written to the output stream.</p>
<h5>Params</h5>
<ul>
<li><a name="method_output_stream.splice.self"><code>self</code></a>: borrow&lt;<a href="#output_stream"><a href="#output_stream"><code>output-stream</code></a></a>&gt;</li>
<li><a name="method_output_stream.splice.src"><code>src</code></a>: own&lt;<a href="#input_stream"><a href="#input_stream"><code>input-stream</code></a></a>&gt;</li>
<li><a name="method_output_stream.splice.len"><code>len</code></a>: <code>u64</code></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_output_stream.splice.0"></a> result&lt;(<code>u64</code>, <a href="#stream_status"><a href="#stream_status"><code>stream-status</code></a></a>)&gt;</li>
</ul>
<h4><a name="method_output_stream.blocking_splice"><code>[method]output-stream.blocking-splice: func</code></a></h4>
<p>Read from one stream and write to another, with blocking.</p>
<p>This is similar to <code>splice</code>, except that it blocks until at least
one byte can be read.</p>
<h5>Params</h5>
<ul>
<li><a name="method_output_stream.blocking_splice.self"><code>self</code></a>: borrow&lt;<a href="#output_stream"><a href="#output_stream"><code>output-stream</code></a></a>&gt;</li>
<li><a name="method_output_stream.blocking_splice.src"><code>src</code></a>: own&lt;<a href="#input_stream"><a href="#input_stream"><code>input-stream</code></a></a>&gt;</li>
<li><a name="method_output_stream.blocking_splice.len"><code>len</code></a>: <code>u64</code></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_output_stream.blocking_splice.0"></a> result&lt;(<code>u64</code>, <a href="#stream_status"><a href="#stream_status"><code>stream-status</code></a></a>)&gt;</li>
</ul>
<h4><a name="method_output_stream.forward"><code>[method]output-stream.forward: func</code></a></h4>
<p>Forward the entire contents of an input stream to an output stream.</p>
<p>This function repeatedly reads from the input stream and writes
the data to the output stream, until the end of the input stream
is reached, or an error is encountered.</p>
<p>Unlike other I/O functions, this function blocks until the end
of the input stream is seen and all the data has been written to
the output stream.</p>
<p>This function returns the number of bytes transferred, and the status of
the output stream.</p>
<h5>Params</h5>
<ul>
<li><a name="method_output_stream.forward.self"><code>self</code></a>: borrow&lt;<a href="#output_stream"><a href="#output_stream"><code>output-stream</code></a></a>&gt;</li>
<li><a name="method_output_stream.forward.src"><code>src</code></a>: own&lt;<a href="#input_stream"><a href="#input_stream"><code>input-stream</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_output_stream.forward.0"></a> result&lt;(<code>u64</code>, <a href="#stream_status"><a href="#stream_status"><code>stream-status</code></a></a>)&gt;</li>
</ul>
<h2><a name="wasi:cli_stdout">Import interface wasi:cli/stdout</a></h2>
<hr />
<h3>Types</h3>
<h4><a name="output_stream"><code>type output-stream</code></a></h4>
<p><a href="#output_stream"><a href="#output_stream"><code>output-stream</code></a></a></p>
<p>
----
<h3>Functions</h3>
<h4><a name="get_stdout"><code>get-stdout: func</code></a></h4>
<h5>Return values</h5>
<ul>
<li><a name="get_stdout.0"></a> own&lt;<a href="#output_stream"><a href="#output_stream"><code>output-stream</code></a></a>&gt;</li>
</ul>
<h2><a name="wasi:cli_stderr">Import interface wasi:cli/stderr</a></h2>
<hr />
<h3>Types</h3>
<h4><a name="output_stream"><code>type output-stream</code></a></h4>
<p><a href="#output_stream"><a href="#output_stream"><code>output-stream</code></a></a></p>
<p>
----
<h3>Functions</h3>
<h4><a name="get_stderr"><code>get-stderr: func</code></a></h4>
<h5>Return values</h5>
<ul>
<li><a name="get_stderr.0"></a> own&lt;<a href="#output_stream"><a href="#output_stream"><code>output-stream</code></a></a>&gt;</li>
</ul>
<h2><a name="wasi:cli_stdin">Import interface wasi:cli/stdin</a></h2>
<hr />
<h3>Types</h3>
<h4><a name="input_stream"><code>type input-stream</code></a></h4>
<p><a href="#input_stream"><a href="#input_stream"><code>input-stream</code></a></a></p>
<p>
----
<h3>Functions</h3>
<h4><a name="get_stdin"><code>get-stdin: func</code></a></h4>
<h5>Return values</h5>
<ul>
<li><a name="get_stdin.0"></a> own&lt;<a href="#input_stream"><a href="#input_stream"><code>input-stream</code></a></a>&gt;</li>
</ul>
<h2><a name="wasi:http_types">Import interface wasi:http/types</a></h2>
<hr />
<h3>Types</h3>
<h4><a name="input_stream"><code>type input-stream</code></a></h4>
<p><a href="#input_stream"><a href="#input_stream"><code>input-stream</code></a></a></p>
<p>
#### <a name="output_stream">`type output-stream`</a>
[`output-stream`](#output_stream)
<p>
#### <a name="pollable">`type pollable`</a>
[`pollable`](#pollable)
<p>
#### <a name="method">`variant method`</a>
<h5>Variant Cases</h5>
<ul>
<li><a name="method.get"><code>get</code></a></li>
<li><a name="method.head"><code>head</code></a></li>
<li><a name="method.post"><code>post</code></a></li>
<li><a name="method.put"><code>put</code></a></li>
<li><a name="method.delete"><code>delete</code></a></li>
<li><a name="method.connect"><code>connect</code></a></li>
<li><a name="method.options"><code>options</code></a></li>
<li><a name="method.trace"><code>trace</code></a></li>
<li><a name="method.patch"><code>patch</code></a></li>
<li><a name="method.other"><code>other</code></a>: <code>string</code></li>
</ul>
<h4><a name="scheme"><code>variant scheme</code></a></h4>
<h5>Variant Cases</h5>
<ul>
<li><a name="scheme.http"><code>HTTP</code></a></li>
<li><a name="scheme.https"><code>HTTPS</code></a></li>
<li><a name="scheme.other"><code>other</code></a>: <code>string</code></li>
</ul>
<h4><a name="error"><code>variant error</code></a></h4>
<h5>Variant Cases</h5>
<ul>
<li><a name="error.invalid_url"><code>invalid-url</code></a>: <code>string</code></li>
<li><a name="error.timeout_error"><code>timeout-error</code></a>: <code>string</code></li>
<li><a name="error.protocol_error"><code>protocol-error</code></a>: <code>string</code></li>
<li><a name="error.unexpected_error"><code>unexpected-error</code></a>: <code>string</code></li>
</ul>
<h4><a name="fields"><code>type fields</code></a></h4>
<p><code>u32</code></p>
<p>
#### <a name="headers">`type headers`</a>
[`fields`](#fields)
<p>
#### <a name="trailers">`type trailers`</a>
[`fields`](#fields)
<p>
#### <a name="incoming_stream">`type incoming-stream`</a>
[`input-stream`](#input_stream)
<p>
#### <a name="outgoing_stream">`type outgoing-stream`</a>
[`output-stream`](#output_stream)
<p>
#### <a name="future_trailers">`type future-trailers`</a>
`u32`
<p>
#### <a name="future_write_trailers_result">`type future-write-trailers-result`</a>
`u32`
<p>
#### <a name="incoming_request">`type incoming-request`</a>
`u32`
<p>
#### <a name="outgoing_request">`type outgoing-request`</a>
`u32`
<p>
#### <a name="request_options">`record request-options`</a>
<h5>Record Fields</h5>
<ul>
<li><a name="request_options.connect_timeout_ms"><code>connect-timeout-ms</code></a>: option&lt;<code>u32</code>&gt;</li>
<li><a name="request_options.first_byte_timeout_ms"><code>first-byte-timeout-ms</code></a>: option&lt;<code>u32</code>&gt;</li>
<li><a name="request_options.between_bytes_timeout_ms"><code>between-bytes-timeout-ms</code></a>: option&lt;<code>u32</code>&gt;</li>
</ul>
<h4><a name="response_outparam"><code>type response-outparam</code></a></h4>
<p><code>u32</code></p>
<p>
#### <a name="status_code">`type status-code`</a>
`u16`
<p>
#### <a name="incoming_response">`type incoming-response`</a>
`u32`
<p>
#### <a name="outgoing_response">`type outgoing-response`</a>
`u32`
<p>
#### <a name="future_incoming_response">`type future-incoming-response`</a>
`u32`
<p>
----
<h3>Functions</h3>
<h4><a name="drop_fields"><code>drop-fields: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="drop_fields.fields"><a href="#fields"><code>fields</code></a></a>: <a href="#fields"><a href="#fields"><code>fields</code></a></a></li>
</ul>
<h4><a name="new_fields"><code>new-fields: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="new_fields.entries"><code>entries</code></a>: list&lt;(<code>string</code>, list&lt;<code>u8</code>&gt;)&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="new_fields.0"></a> <a href="#fields"><a href="#fields"><code>fields</code></a></a></li>
</ul>
<h4><a name="fields_get"><code>fields-get: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="fields_get.fields"><a href="#fields"><code>fields</code></a></a>: <a href="#fields"><a href="#fields"><code>fields</code></a></a></li>
<li><a name="fields_get.name"><code>name</code></a>: <code>string</code></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="fields_get.0"></a> list&lt;list&lt;<code>u8</code>&gt;&gt;</li>
</ul>
<h4><a name="fields_set"><code>fields-set: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="fields_set.fields"><a href="#fields"><code>fields</code></a></a>: <a href="#fields"><a href="#fields"><code>fields</code></a></a></li>
<li><a name="fields_set.name"><code>name</code></a>: <code>string</code></li>
<li><a name="fields_set.value"><code>value</code></a>: list&lt;list&lt;<code>u8</code>&gt;&gt;</li>
</ul>
<h4><a name="fields_delete"><code>fields-delete: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="fields_delete.fields"><a href="#fields"><code>fields</code></a></a>: <a href="#fields"><a href="#fields"><code>fields</code></a></a></li>
<li><a name="fields_delete.name"><code>name</code></a>: <code>string</code></li>
</ul>
<h4><a name="fields_append"><code>fields-append: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="fields_append.fields"><a href="#fields"><code>fields</code></a></a>: <a href="#fields"><a href="#fields"><code>fields</code></a></a></li>
<li><a name="fields_append.name"><code>name</code></a>: <code>string</code></li>
<li><a name="fields_append.value"><code>value</code></a>: list&lt;<code>u8</code>&gt;</li>
</ul>
<h4><a name="fields_entries"><code>fields-entries: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="fields_entries.fields"><a href="#fields"><code>fields</code></a></a>: <a href="#fields"><a href="#fields"><code>fields</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="fields_entries.0"></a> list&lt;(<code>string</code>, list&lt;<code>u8</code>&gt;)&gt;</li>
</ul>
<h4><a name="fields_clone"><code>fields-clone: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="fields_clone.fields"><a href="#fields"><code>fields</code></a></a>: <a href="#fields"><a href="#fields"><code>fields</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="fields_clone.0"></a> <a href="#fields"><a href="#fields"><code>fields</code></a></a></li>
</ul>
<h4><a name="finish_incoming_stream"><code>finish-incoming-stream: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="finish_incoming_stream.s"><code>s</code></a>: own&lt;<a href="#incoming_stream"><a href="#incoming_stream"><code>incoming-stream</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="finish_incoming_stream.0"></a> option&lt;<a href="#future_trailers"><a href="#future_trailers"><code>future-trailers</code></a></a>&gt;</li>
</ul>
<h4><a name="finish_outgoing_stream"><code>finish-outgoing-stream: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="finish_outgoing_stream.s"><code>s</code></a>: own&lt;<a href="#outgoing_stream"><a href="#outgoing_stream"><code>outgoing-stream</code></a></a>&gt;</li>
</ul>
<h4><a name="finish_outgoing_stream_with_trailers"><code>finish-outgoing-stream-with-trailers: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="finish_outgoing_stream_with_trailers.s"><code>s</code></a>: own&lt;<a href="#outgoing_stream"><a href="#outgoing_stream"><code>outgoing-stream</code></a></a>&gt;</li>
<li><a name="finish_outgoing_stream_with_trailers.trailers"><a href="#trailers"><code>trailers</code></a></a>: <a href="#trailers"><a href="#trailers"><code>trailers</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="finish_outgoing_stream_with_trailers.0"></a> <a href="#future_write_trailers_result"><a href="#future_write_trailers_result"><code>future-write-trailers-result</code></a></a></li>
</ul>
<h4><a name="drop_future_trailers"><code>drop-future-trailers: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="drop_future_trailers.f"><code>f</code></a>: <a href="#future_trailers"><a href="#future_trailers"><code>future-trailers</code></a></a></li>
</ul>
<h4><a name="future_trailers_get"><code>future-trailers-get: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="future_trailers_get.f"><code>f</code></a>: <a href="#future_trailers"><a href="#future_trailers"><code>future-trailers</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="future_trailers_get.0"></a> option&lt;result&lt;<a href="#trailers"><a href="#trailers"><code>trailers</code></a></a>, <a href="#error"><a href="#error"><code>error</code></a></a>&gt;&gt;</li>
</ul>
<h4><a name="listen_to_future_trailers"><code>listen-to-future-trailers: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="listen_to_future_trailers.f"><code>f</code></a>: <a href="#future_trailers"><a href="#future_trailers"><code>future-trailers</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="listen_to_future_trailers.0"></a> own&lt;<a href="#pollable"><a href="#pollable"><code>pollable</code></a></a>&gt;</li>
</ul>
<h4><a name="drop_future_write_trailers_result"><code>drop-future-write-trailers-result: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="drop_future_write_trailers_result.f"><code>f</code></a>: <a href="#future_write_trailers_result"><a href="#future_write_trailers_result"><code>future-write-trailers-result</code></a></a></li>
</ul>
<h4><a name="future_write_trailers_result_get"><code>future-write-trailers-result-get: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="future_write_trailers_result_get.f"><code>f</code></a>: <a href="#future_write_trailers_result"><a href="#future_write_trailers_result"><code>future-write-trailers-result</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="future_write_trailers_result_get.0"></a> option&lt;result&lt;_, <a href="#error"><a href="#error"><code>error</code></a></a>&gt;&gt;</li>
</ul>
<h4><a name="listen_to_future_write_trailers_result"><code>listen-to-future-write-trailers-result: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="listen_to_future_write_trailers_result.f"><code>f</code></a>: <a href="#future_write_trailers_result"><a href="#future_write_trailers_result"><code>future-write-trailers-result</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="listen_to_future_write_trailers_result.0"></a> own&lt;<a href="#pollable"><a href="#pollable"><code>pollable</code></a></a>&gt;</li>
</ul>
<h4><a name="drop_incoming_request"><code>drop-incoming-request: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="drop_incoming_request.request"><code>request</code></a>: <a href="#incoming_request"><a href="#incoming_request"><code>incoming-request</code></a></a></li>
</ul>
<h4><a name="drop_outgoing_request"><code>drop-outgoing-request: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="drop_outgoing_request.request"><code>request</code></a>: <a href="#outgoing_request"><a href="#outgoing_request"><code>outgoing-request</code></a></a></li>
</ul>
<h4><a name="incoming_request_method"><code>incoming-request-method: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="incoming_request_method.request"><code>request</code></a>: <a href="#incoming_request"><a href="#incoming_request"><code>incoming-request</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="incoming_request_method.0"></a> <a href="#method"><a href="#method"><code>method</code></a></a></li>
</ul>
<h4><a name="incoming_request_path_with_query"><code>incoming-request-path-with-query: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="incoming_request_path_with_query.request"><code>request</code></a>: <a href="#incoming_request"><a href="#incoming_request"><code>incoming-request</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="incoming_request_path_with_query.0"></a> option&lt;<code>string</code>&gt;</li>
</ul>
<h4><a name="incoming_request_scheme"><code>incoming-request-scheme: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="incoming_request_scheme.request"><code>request</code></a>: <a href="#incoming_request"><a href="#incoming_request"><code>incoming-request</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="incoming_request_scheme.0"></a> option&lt;<a href="#scheme"><a href="#scheme"><code>scheme</code></a></a>&gt;</li>
</ul>
<h4><a name="incoming_request_authority"><code>incoming-request-authority: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="incoming_request_authority.request"><code>request</code></a>: <a href="#incoming_request"><a href="#incoming_request"><code>incoming-request</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="incoming_request_authority.0"></a> option&lt;<code>string</code>&gt;</li>
</ul>
<h4><a name="incoming_request_headers"><code>incoming-request-headers: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="incoming_request_headers.request"><code>request</code></a>: <a href="#incoming_request"><a href="#incoming_request"><code>incoming-request</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="incoming_request_headers.0"></a> <a href="#headers"><a href="#headers"><code>headers</code></a></a></li>
</ul>
<h4><a name="incoming_request_consume"><code>incoming-request-consume: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="incoming_request_consume.request"><code>request</code></a>: <a href="#incoming_request"><a href="#incoming_request"><code>incoming-request</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="incoming_request_consume.0"></a> result&lt;own&lt;<a href="#incoming_stream"><a href="#incoming_stream"><code>incoming-stream</code></a></a>&gt;&gt;</li>
</ul>
<h4><a name="new_outgoing_request"><code>new-outgoing-request: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="new_outgoing_request.method"><a href="#method"><code>method</code></a></a>: <a href="#method"><a href="#method"><code>method</code></a></a></li>
<li><a name="new_outgoing_request.path_with_query"><code>path-with-query</code></a>: option&lt;<code>string</code>&gt;</li>
<li><a name="new_outgoing_request.scheme"><a href="#scheme"><code>scheme</code></a></a>: option&lt;<a href="#scheme"><a href="#scheme"><code>scheme</code></a></a>&gt;</li>
<li><a name="new_outgoing_request.authority"><code>authority</code></a>: option&lt;<code>string</code>&gt;</li>
<li><a name="new_outgoing_request.headers"><a href="#headers"><code>headers</code></a></a>: <a href="#headers"><a href="#headers"><code>headers</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="new_outgoing_request.0"></a> result&lt;<a href="#outgoing_request"><a href="#outgoing_request"><code>outgoing-request</code></a></a>, <a href="#error"><a href="#error"><code>error</code></a></a>&gt;</li>
</ul>
<h4><a name="outgoing_request_write"><code>outgoing-request-write: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="outgoing_request_write.request"><code>request</code></a>: <a href="#outgoing_request"><a href="#outgoing_request"><code>outgoing-request</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="outgoing_request_write.0"></a> result&lt;own&lt;<a href="#outgoing_stream"><a href="#outgoing_stream"><code>outgoing-stream</code></a></a>&gt;&gt;</li>
</ul>
<h4><a name="drop_response_outparam"><code>drop-response-outparam: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="drop_response_outparam.response"><code>response</code></a>: <a href="#response_outparam"><a href="#response_outparam"><code>response-outparam</code></a></a></li>
</ul>
<h4><a name="set_response_outparam"><code>set-response-outparam: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="set_response_outparam.param"><code>param</code></a>: <a href="#response_outparam"><a href="#response_outparam"><code>response-outparam</code></a></a></li>
<li><a name="set_response_outparam.response"><code>response</code></a>: result&lt;<a href="#outgoing_response"><a href="#outgoing_response"><code>outgoing-response</code></a></a>, <a href="#error"><a href="#error"><code>error</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="set_response_outparam.0"></a> result</li>
</ul>
<h4><a name="drop_incoming_response"><code>drop-incoming-response: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="drop_incoming_response.response"><code>response</code></a>: <a href="#incoming_response"><a href="#incoming_response"><code>incoming-response</code></a></a></li>
</ul>
<h4><a name="drop_outgoing_response"><code>drop-outgoing-response: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="drop_outgoing_response.response"><code>response</code></a>: <a href="#outgoing_response"><a href="#outgoing_response"><code>outgoing-response</code></a></a></li>
</ul>
<h4><a name="incoming_response_status"><code>incoming-response-status: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="incoming_response_status.response"><code>response</code></a>: <a href="#incoming_response"><a href="#incoming_response"><code>incoming-response</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="incoming_response_status.0"></a> <a href="#status_code"><a href="#status_code"><code>status-code</code></a></a></li>
</ul>
<h4><a name="incoming_response_headers"><code>incoming-response-headers: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="incoming_response_headers.response"><code>response</code></a>: <a href="#incoming_response"><a href="#incoming_response"><code>incoming-response</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="incoming_response_headers.0"></a> <a href="#headers"><a href="#headers"><code>headers</code></a></a></li>
</ul>
<h4><a name="incoming_response_consume"><code>incoming-response-consume: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="incoming_response_consume.response"><code>response</code></a>: <a href="#incoming_response"><a href="#incoming_response"><code>incoming-response</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="incoming_response_consume.0"></a> result&lt;own&lt;<a href="#incoming_stream"><a href="#incoming_stream"><code>incoming-stream</code></a></a>&gt;&gt;</li>
</ul>
<h4><a name="new_outgoing_response"><code>new-outgoing-response: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="new_outgoing_response.status_code"><a href="#status_code"><code>status-code</code></a></a>: <a href="#status_code"><a href="#status_code"><code>status-code</code></a></a></li>
<li><a name="new_outgoing_response.headers"><a href="#headers"><code>headers</code></a></a>: <a href="#headers"><a href="#headers"><code>headers</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="new_outgoing_response.0"></a> result&lt;<a href="#outgoing_response"><a href="#outgoing_response"><code>outgoing-response</code></a></a>, <a href="#error"><a href="#error"><code>error</code></a></a>&gt;</li>
</ul>
<h4><a name="outgoing_response_write"><code>outgoing-response-write: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="outgoing_response_write.response"><code>response</code></a>: <a href="#outgoing_response"><a href="#outgoing_response"><code>outgoing-response</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="outgoing_response_write.0"></a> result&lt;own&lt;<a href="#outgoing_stream"><a href="#outgoing_stream"><code>outgoing-stream</code></a></a>&gt;&gt;</li>
</ul>
<h4><a name="drop_future_incoming_response"><code>drop-future-incoming-response: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="drop_future_incoming_response.f"><code>f</code></a>: <a href="#future_incoming_response"><a href="#future_incoming_response"><code>future-incoming-response</code></a></a></li>
</ul>
<h4><a name="future_incoming_response_get"><code>future-incoming-response-get: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="future_incoming_response_get.f"><code>f</code></a>: <a href="#future_incoming_response"><a href="#future_incoming_response"><code>future-incoming-response</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="future_incoming_response_get.0"></a> option&lt;result&lt;<a href="#incoming_response"><a href="#incoming_response"><code>incoming-response</code></a></a>, <a href="#error"><a href="#error"><code>error</code></a></a>&gt;&gt;</li>
</ul>
<h4><a name="listen_to_future_incoming_response"><code>listen-to-future-incoming-response: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="listen_to_future_incoming_response.f"><code>f</code></a>: <a href="#future_incoming_response"><a href="#future_incoming_response"><code>future-incoming-response</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="listen_to_future_incoming_response.0"></a> own&lt;<a href="#pollable"><a href="#pollable"><code>pollable</code></a></a>&gt;</li>
</ul>
<h2><a name="wasi:http_outgoing_handler">Import interface wasi:http/outgoing-handler</a></h2>
<hr />
<h3>Types</h3>
<h4><a name="outgoing_request"><code>type outgoing-request</code></a></h4>
<p><a href="#outgoing_request"><a href="#outgoing_request"><code>outgoing-request</code></a></a></p>
<p>
#### <a name="request_options">`type request-options`</a>
[`request-options`](#request_options)
<p>
#### <a name="future_incoming_response">`type future-incoming-response`</a>
[`future-incoming-response`](#future_incoming_response)
<p>
----
<h3>Functions</h3>
<h4><a name="handle"><code>handle: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="handle.request"><code>request</code></a>: <a href="#outgoing_request"><a href="#outgoing_request"><code>outgoing-request</code></a></a></li>
<li><a name="handle.options"><code>options</code></a>: option&lt;<a href="#request_options"><a href="#request_options"><code>request-options</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="handle.0"></a> <a href="#future_incoming_response"><a href="#future_incoming_response"><code>future-incoming-response</code></a></a></li>
</ul>
<h2><a name="wasi:http_incoming_handler">Export interface wasi:http/incoming-handler</a></h2>
<hr />
<h3>Types</h3>
<h4><a name="incoming_request"><code>type incoming-request</code></a></h4>
<p><a href="#incoming_request"><a href="#incoming_request"><code>incoming-request</code></a></a></p>
<p>
#### <a name="response_outparam">`type response-outparam`</a>
[`response-outparam`](#response_outparam)
<p>
----
<h3>Functions</h3>
<h4><a name="handle"><code>handle: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="handle.request"><code>request</code></a>: <a href="#incoming_request"><a href="#incoming_request"><code>incoming-request</code></a></a></li>
<li><a name="handle.response_out"><code>response-out</code></a>: <a href="#response_outparam"><a href="#response_outparam"><code>response-outparam</code></a></a></li>
</ul>
