# Báo Cáo Thực Tập
* Tìm hiểu về KVM Hypervisor
* Tuần 
* Ngày: 09/03/2018
* Người tạo: Nguyễn Xuân Lưu
*********************************
## Phân Tích Cấu trúc dữ liệu các đối tượng trong Qemu-KVM
### 1. KVMState
File mã nguồn: \kvm-all.c

Cấu trúc KVMState chứa các tệp thông tin về máy ảo trong Qemu
```c
struct KVMState
{
    AccelState parent_obj;

    int nr_slots;
    int fd;
    int vmfd;
    int coalesced_mmio;
    struct kvm_coalesced_mmio_ring *coalesced_mmio_ring;
    bool coalesced_flush_in_progress;
    int broken_set_mem_region;
    int vcpu_events;
    int robust_singlestep;
    int debugregs;
#ifdef KVM_CAP_SET_GUEST_DEBUG
    struct kvm_sw_breakpoint_head kvm_sw_breakpoints;
#endif
    int many_ioeventfds;
    int intx_set_mask;
    /* The man page (and posix) say ioctl numbers are signed int, but
     * they're not.  Linux, glibc and *BSD all treat ioctl numbers as
     * unsigned, and treating them as signed here can break things */
    unsigned irq_set_ioctl;
    unsigned int sigmask_len;
    GHashTable *gsimap;
#ifdef KVM_CAP_IRQ_ROUTING
    struct kvm_irq_routing *irq_routes;
    int nr_allocated_irq_routes;
    uint32_t *used_gsi_bitmap;
    unsigned int gsi_count;
    QTAILQ_HEAD(msi_hashtab, KVMMSIRoute) msi_hashtab[KVM_MSI_HASHTAB_SIZE];
#endif
    KVMMemoryListener memory_listener;
};
```

### 2. CPUX86State
File mã nguồn: \target-i386\cpu.h

