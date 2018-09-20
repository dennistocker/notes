# The Boost C++ Libraries

## Chapter 21 Boost.optional


## Chapter 32 Boost.Asio

### I/O Services

1. Every program based on Boost.Asio uses an object of type `boost::asio::io_context`(io_service).

### I/O Objects
1. `boost::asio::ip::tcp::socket`
2. `boost::asio::serial_port`
3. `boost::asio::steady_timer`

## Chapter 36 Boost.Signals2

The Boost.Signals2 library is an implementation of a managed signals and slots system.

### Signal

1. connect(), Slots are invoked this order
    1. ungrouped slots connected with boost::signals2::at_front
    2. grouped slots according to ordering of their groups
    3. ungrouped slots connected with boost::signals2:at_back
    4. by default slots are connected at the end of the slot list
2. disconnect()
3. num_slots()
4. empty()
5. disconnect_all_slots()
6. Both return values were correctly accepted by `s`, but all except the last one were ignored.
7. `s()` does not directly return the result of the last function called. An object of type `boost::optional` is returned, use `*` to deference.
8. To process all the values returned by the handlers, a combiner must be passed to `boost::signals::signal` as a second tmplate parameter.

### Connections

1. `connect()` returns an object of type `boost::signals2::connection`
2. disconnect()
3. shared_conenction_block
    1. block()
    2. blocking()
    3. unblock()
    4. the block will be remove automatically once the `shared_conenction_block` go out of scope.
4. Scoped Connections
    1. The `boost::signals2::scoped_connection` will be disconnected when the `scoped_connection` class goes out of scope.
    2. Attempts to initialize a scoped_connection with the assignment syntax will fall due to it being noncopyable.
    3. Either the explicit initialization syntax or default construction followed by assignment from a `signals2::connection` will work.
5. Automatic Connection Management
    1. Boost.Signals2 can release a connection once the object whose member function is associated with a signal is destroyed. need to use `slot_type` instead of `std::bind`
    2. shared_ptr classes other than `boost::shared_ptr` (such as `std::shared_ptr`) may also be tracked fro connection management purposes. They are supported by the `slot::track_foreign` method.
6. when can disconnections occur
    1. disconnect
        1. signal's `disconnect`
        2. connection's `disconnect`
        3. scoped_connection's desctructor
    2. object tracked by the slot is destroyed
    3. signal is destroyed
7. Boost.Signals2 suportes associating functions to signal when a signal is triggered, the new associations will only be used when the signal is triggered again. Releasing connections in an event handler is the same.

### Multithreading

1. `boost::signals2::signal` and `boost::signals2::connection` are thread safe.
2. `boost::signals2::shared_conenction_block` is not thread safe.
3. For single-threaded applications, support for multithreading can be disabled in Boost.Signals2.
