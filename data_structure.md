# Task 3: Data Structure Investigation
The objective of this task is to document all internal data structures defined in io_uring. 

Structure name | Defined in | Attributes | Caller Functions Source | source caller | usage
---------------|------------|------------|-------------------------|---------------|-------------------
io_ev_fd       | io_uring/eventfd.c | eventfd_ctx, uint, uint, refcount_t, atomic_t, rcu_head | io_eventfd_free | io_uring/eventfd.c | local variable
| | | | io_eventfd_put | io_uring/eventfd.c | function parameter
| | | | io_eventfd_do_signal | io_uring/eventfd.c | local variable, function parameter
| | | | __io_eventfd_signal | io_uring/eventfd.c | function parameter
| | | | io_eventfd_grab | io_uring/eventfd.c | return value, local variable
| | | | io_eventfd_signal | io_uring/eventfd.c | local variable 
| | | | io_eventfd_flush_signal | io_uring/eventfd.c | local variable
| | | | io_eventfd_register | io_uring/eventfd.c | local variable
| | | | io_eventfd_unregister | io_uring/eventfd.c | function parameter
io_meta_state  | io_uring/rw.h | u32 seed, iov_iter_state iter_meta                                                                                              | Used in direct I/O routines (via io_async_rw)              | io_uring/rw.h  | Holds metadata for direct I/O operations                        
io_async_rw    | io_uring/rw.h | iou_vec vec, size_t bytes_done, iov_iter iter, iov_iter_state iter_state, iovec fast_iov, union { wait_page_queue wpq; or { uio_meta meta, io_meta_state meta_state } } | Invoked in io_prep_read/write and related async routines  | io_uring/rw.h  | Provides context for asynchronous I/O (supports buffered & direct modes)
io_wq_work_list| io_uring/slist.h   | io_wq_work_node *first, io_wq_work_node *last            | wq_list_add_tail, wq_list_add_head, wq_list_cut   | io_uring/slist.h   | Manages the linked list of work nodes                 
io_wq_work_node| io_uring/slist.h   | io_wq_work_node *next                                   | wq_list_add_after, wq_stack_add_head, wq_stack_extract  | io_uring/slist.h   | Represents a node element in the work queue list      
io_wq_work     | io_uring/slist.h   | io_wq_work_node list                                   | wq_next_work                                    | io_uring/slist.h   | Encapsulates a work queue item (links work nodes)    
io_splice      | io_uring/splice.c    | file *file_out, loff_t off_out, loff_t off_in, u64 len, int splice_fd_in, unsigned int flags, io_rsrc_node *rsrc_node | __io_splice_prep, io_tee_prep, io_splice_cleanup, io_splice_get_file, io_tee, io_splice_prep, io_splice | io_uring/splice.c  | Used as a function parameter
io_tee_prep        | io_uring/splice.h    | (struct io_kiocb *req, const struct io_uring_sqe *sqe) -> int       | Tee operations                 | io_uring/splice.h   | Prepares tee
io_tee             | io_uring/splice.h    | (struct io_kiocb *req, unsigned int issue_flags) -> int            | Tee operations                 | io_uring/splice.h   | Executes tee
io_splice_cleanup  | io_uring/splice.h    | (struct io_kiocb *req) -> void                                    | Splice cleanup                 | io_uring/splice.h   | Cleanup
io_splice_prep     | io_uring/splice.h    | (struct io_kiocb *req, const struct io_uring_sqe *sqe) -> int       | Splice operations              | io_uring/splice.h   | Prepares splice
io_splice          | io_uring/splice.h    | (struct io_kiocb *req, unsigned int issue_flags) -> int            | Splice operations              | io_uring/splice.h   | Executes splice
io_sq_thread_unpark     | io_uring/sqpoll.c    | (struct io_sq_data *sqd) -> void                              | SQ event handling                                   | io_uring/sqpoll.c      | Unparks SQ thread
io_sq_thread_park       | io_uring/sqpoll.c    | (struct io_sq_data *sqd) -> void                              | SQ thread management                                | io_uring/sqpoll.c      | Parks SQ thread
io_sq_thread_stop       | io_uring/sqpoll.c    | (struct io_sq_data *sqd) -> void                              | Signals thread stop                                 | io_uring/sqpoll.c      | Stops SQ thread
io_put_sq_data          | io_uring/sqpoll.c    | (struct io_sq_data *sqd) -> void                              | Releases and cleans up SQ data                      | io_uring/sqpoll.c      | Reclaims SQ data
io_sqd_update_thread_idle| io_uring/sqpoll.c   | (struct io_sq_data *sqd) -> void                              | Updates idle time from context list               | io_uring/sqpoll.c      | Updates idle time
io_sq_thread_finish     | io_uring/sqpoll.c    | (struct io_ring_ctx *ctx) -> void                             | Cleans up SQ thread for a given context             | io_uring/sqpoll.c      | Finishes SQ thread
io_attach_sq_data       | io_uring/sqpoll.c    | (struct io_uring_params *p) -> struct io_sq_data*              | Attaches SQ data from existing FD                   | io_uring/sqpoll.c      | Attaches SQ data
io_get_sq_data          | io_uring/sqpoll.c    | (struct io_uring_params *p, bool *attached) -> struct io_sq_data*| Retrieves or allocates SQ data                      | io_uring/sqpoll.c      | Gets/allocates SQ data
io_sqd_events_pending   | io_uring/sqpoll.c    | (struct io_sq_data *sqd) -> bool                              | Checks SQ event flags                                | io_uring/sqpoll.c      | Checks events
io_sq_thread          | io_uring/sqpoll.c    | (struct io_ring_ctx *ctx, bool cap_entries) -> int             | Submits SQ entries with cap if needed              | io_uring/sqpoll.c      | Submits SQEs
io_sqd_handle_event     | io_uring/sqpoll.c    | (struct io_sq_data *sqd) -> bool                              | Processes pending SQ events                         | io_uring/sqpoll.c      | Processes events
io_sq_tw                | io_uring/sqpoll.c    | (struct llist_node **retry_list, int max_entries) -> unsigned int| Processes task work from retry list                | io_uring/sqpoll.c      | Runs task work
io_sq_tw_pending        | io_uring/sqpoll.c    | (struct llist_node *retry_list) -> bool                        | Checks for pending task work                        | io_uring/sqpoll.c      | Checks task work
io_sq_update_worktime   | io_uring/sqpoll.c    | (struct io_sq_data *sqd, struct rusage *start) -> void          | Updates work time based on rusage                   | io_uring/sqpoll.c      | Updates work time
io_sq_thread            | io_uring/sqpoll.c    | (void *data) -> int                                             | Main SQ polling loop (executes submissions/task work)| io_uring/sqpoll.c      | SQ thread loop
io_sqpoll_wait_sq         | io_uring/sqpoll.c    | (struct io_ring_ctx *ctx) -> void                                       | Internal SQ poll routines                 | io_uring/sqpoll.c      | Waits for SQ avail.
io_sq_offload_create      | io_uring/sqpoll.c    | (struct io_ring_ctx *ctx, struct io_uring_params *p) -> int               | SQ poll setup/offload                      | io_uring/sqpoll.c      | Creates SQ poll thread.
io_sqpoll_wq_cpu_affinity | io_uring/sqpoll.c    | (struct io_ring_ctx *ctx, cpumask_var_t mask) -> int                      | SQ poll CPU affinity config                | io_uring/sqpoll.c      | Sets CPU affinity.
io_sq_data     | io_uring/sqpoll.h  | refcount_t refs, atomic_t park_pending, struct mutex lock, struct list_head ctx_list, struct task_struct *thread, struct wait_queue_head wait, unsigned sq_thread_idle, int sq_cpu, pid_t task_pid, pid_t task_tgid, u64 work_time, unsigned long state, struct completion exited | Used by SQ polling functions  | io_uring/sqpoll.h   | SQ polling data
io_sq_offload_create         | io_uring/sqpoll.h  | (struct io_ring_ctx *ctx, struct io_uring_params *p) -> int              | SQ poll setup                | io_uring/sqpoll.h   | Create offload
io_sq_thread_finish          | io_uring/sqpoll.h  | (struct io_ring_ctx *ctx) -> void                                      | SQ cleanup                   | io_uring/sqpoll.h   | Finish thread
io_sq_thread_stop            | io_uring/sqpoll.h  | (struct io_sq_data *sqd) -> void                                       | SQ stop                      | io_uring/sqpoll.h   | Stop thread
io_sq_thread_park            | io_uring/sqpoll.h  | (struct io_sq_data *sqd) -> void                                       | SQ thread control            | io_uring/sqpoll.h   | Park thread
io_sq_thread_unpark          | io_uring/sqpoll.h  | (struct io_sq_data *sqd) -> void                                       | SQ thread control            | io_uring/sqpoll.h   | Unpark thread
io_put_sq_data               | io_uring/sqpoll.h  | (struct io_sq_data *sqd) -> void                                       | SQ data cleanup              | io_uring/sqpoll.h   | Release data
io_sqpoll_wait_sq            | io_uring/sqpoll.h  | (struct io_ring_ctx *ctx) -> void                                      | SQ polling wait              | io_uring/sqpoll.h   | Wait for SQ
io_sqpoll_wq_cpu_affinity    | io_uring/sqpoll.h  | (struct io_ring_ctx *ctx, cpumask_var_t mask) -> int                     | SQ CPU affinity              | io_uring/sqpoll.h   | Set CPU affinity
io_statx       | io_uring/statx.c   | file *file, int dfd, unsigned int mask, unsigned int flags, filename *filename, statx __user *buffer | io_statx_prep, io_statx, io_statx_cleanup             | io_uring/statx.c     | Request details
io_statx_prep      | io_uring/statx.c   | (struct io_kiocb *req, const struct io_uring_sqe *sqe) -> int                  | Prepares statx request       | io_uring/statx.c     | Prep statx
io_statx           | io_uring/statx.c   | (struct io_kiocb *req, unsigned int issue_flags) -> int                       | Executes statx syscall       | io_uring/statx.c     | Execute statx
io_statx_cleanup   | io_uring/statx.c   | (struct io_kiocb *req) -> void                                                 | Cleans up statx request      | io_uring/statx.c     | Cleanup statx
io_statx_prep      | io_uring/statx.h    | (struct io_kiocb *req, const struct io_uring_sqe *sqe) -> int               | statx operations          | io_uring/statx.h    | Prep statx
io_statx           | io_uring/statx.h    | (struct io_kiocb *req, unsigned int issue_flags) -> int                    | statx operations          | io_uring/statx.h    | Execute statx
io_statx_cleanup   | io_uring/statx.h    | (struct io_kiocb *req) -> void                                             | statx cleanup             | io_uring/statx.h    | Cleanup statx
io_sync        | io_uring/sync.c    | file *file, loff_t len, loff_t off, int flags, int mode               | io_sfr_prep, io_sync_file_range, io_fsync_prep, io_fsync, io_fallocate_prep, io_fallocate | io_uring/sync.c      | Sync req details
io_sfr_prep           | io_uring/sync.c    | (struct io_kiocb *req, const struct io_uring_sqe *sqe) -> int              | Sync file_range          | io_uring/sync.c     | Prep sync_range
io_sync_file_range    | io_uring/sync.c    | (struct io_kiocb *req, unsigned int issue_flags) -> int                   | Sync file_range          | io_uring/sync.c     | Exec sync_range
io_fsync_prep         | io_uring/sync.c    | (struct io_kiocb *req, const struct io_uring_sqe *sqe) -> int              | Fsync                    | io_uring/sync.c     | Prep fsync
io_fsync              | io_uring/sync.c    | (struct io_kiocb *req, unsigned int issue_flags) -> int                   | Fsync                    | io_uring/sync.c     | Exec fsync
io_fallocate_prep     | io_uring/sync.c    | (struct io_kiocb *req, const struct io_uring_sqe *sqe) -> int              | Fallocate                | io_uring/sync.c     | Prep fallocate
io_fallocate          | io_uring/sync.c    | (struct io_kiocb *req, unsigned int issue_flags) -> int                   | Fallocate                | io_uring/sync.c     | Exec fallocate
io_sfr_prep           | io_uring/sync.h    | (struct io_kiocb *req, const struct io_uring_sqe *sqe) -> int             | Sync range prep              | io_uring/sync.h     | Prep sync range
io_sync_file_range    | io_uring/sync.h    | (struct io_kiocb *req, unsigned int issue_flags) -> int                  | Sync file_range exec         | io_uring/sync.h     | Exec sync range
io_fsync_prep         | io_uring/sync.h    | (struct io_kiocb *req, const struct io_uring_sqe *sqe) -> int             | Fsync prep                   | io_uring/sync.h     | Prep fsync
io_fsync              | io_uring/sync.h    | (struct io_kiocb *req, unsigned int issue_flags) -> int                  | Fsync exec                   | io_uring/sync.h     | Exec fsync
io_fallocate          | io_uring/sync.h    | (struct io_kiocb *req, unsigned int issue_flags) -> int                  | Fallocate exec               | io_uring/sync.h     | Exec fallocate
io_fallocate_prep     | io_uring/sync.h    | (struct io_kiocb *req, const struct io_uring_sqe *sqe) -> int             | Fallocate prep               | io_uring/sync.h     | Prep fallocate
io_tctx_node   | io_uring/tctx.h  | list_head ctx_node, task_struct *task, io_ring_ctx *ctx                        | Used in tctx management  | io_uring/tctx.h  | Maps task & ctx
io_timeout     | io_uring/timeout.c    | file *file, u32 off, u32 target_seq, u32 repeats, list_head list, io_kiocb *head, io_kiocb *prev      | Used by timeout handling functions (e.g., io_timeout_fn, io_timeout_complete, io_flush_timeouts)   | io_uring/timeout.c     | Timeout request metadata 
io_timeout_rem | io_uring/timeout.c    | file *file, u64 addr, timespec64 ts, u32 flags, bool ltimeout                                         | Utilized in timeout update routines                                                             | io_uring/timeout.c     | Holds update details 
io_timeout_data  | io_uring/timeout.h      | io_kiocb *req, hrtimer timer, timespec64 ts, hrtimer_mode mode, u32 flags      | Used by timeout management functions   | io_uring/timeout.h     | Holds timeout state information
io_ftrunc      | io_uring/truncate.c   | file *file, loff_t len               | io_ftruncate_prep, io_ftruncate | io_uring/truncate.c   | Holds file truncation parameters
io_async_cmd   | io_uring/uring_cmd.h      | io_uring_cmd_data data, iou_vec vec, io_uring_sqe sqes[2]          | Utilized in asynchronous command handling | io_uring/uring_cmd.h    | Holds async command details
io_waitid      | io_uring/waitid.c      | file *file, int which, pid_t upid, int options, atomic_t refs, wait_queue_head *head, siginfo __user *infop, waitid_info info | Used by functions handling async waitid notifications (e.g., io_waitid_prep, io_waitid, io_waitid_complete) | io_uring/waitid.c     | Holds waitid notification state
io_waitid_async  | io_uring/waitid.h    | struct io_kiocb *req, struct wait_opts wo       | Used in async waitid processes       | io_uring/waitid.h     | Holds asynchronous waitid data
io_xattr       | io_uring/xattr.c     | file *file, kernel_xattr_ctx ctx, filename *filename                     | Used by xattr operations (get/set xattr functions) | io_uring/xattr.c      | Holds extended attribute context and related data
io_zcrx_args   | io_uring/zcrx.c      | struct io_kiocb *req; struct io_zcrx_ifq *ifq; struct socket *sock; unsigned nr_skbs;  | Used by zcrx functions to pass zero‑copy RX arguments  | io_uring/zcrx.c      | Holds parameters for zcrx operations
io_zcrx_args   | io_uring/zcrx.c      | struct io_kiocb *req; struct io_zcrx_ifq *ifq; struct socket *sock; unsigned nr_skbs;  | Used by zcrx functions to pass zero‑copy RX arguments      | io_uring/zcrx.c      | Holds parameters for zcrx operations
io_zcrx_area   | io_uring/zcrx.h      | struct net_iov_area nia; struct io_zcrx_ifq *ifq; atomic_t *user_refs; bool is_mapped; u16 area_id; struct page **pages; spinlock_t freelist_lock; u32 free_count; u32 *freelist; | Used to manage the zero‑copy receive buffer area and free index list | io_uring/zcrx.h      | Represents the shared memory area for zero‑copy buffers
io_zcrx_ifq    | io_uring/zcrx.h      | struct io_ring_ctx *ctx; struct io_zcrx_area *area; struct io_uring *rq_ring; struct io_uring_zcrx_rqe *rqes; u32 rq_entries; u32 cached_rq_head; spinlock_t rq_lock; u32 if_rxq; struct device *dev; struct net_device *netdev; netdevice_tracker netdev_tracker; spinlock_t lock; | Accessed by zcrx functions for managing zero‑copy RX operations | io_uring/zcrx.h      | Represents the zero‑copy RX interface queue linking io_uring context and network device buffers


If the following row value in a column is missing, assume the value is the same with the previous row in the same column. 
Continue until all data structures documented properly.
