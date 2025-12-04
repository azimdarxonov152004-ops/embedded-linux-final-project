#include <linux/module.h>
#include <linux/kernel.h>
#include <linux/init.h>
#include <linux/fs.h>
#include <linux/uaccess.h>

#define DEVICE_NAME "coursera_final"
#define CLASS_NAME  "coursera"

static int major_number;
static char message[256] = {0};
static short message_size;

static int dev_t dev_num;

static int dev_open(struct inode *inodep, struct file *filep) {
    printk(KERN_INFO "Coursera: Device opened\n");
    return 0;
}

static ssize_t dev_write(struct file *filep, const char *buffer, size_t len, loff_t *offset) {
    sprintf(message, "%s", buffer);
    message_size = strlen(message);
    printk(KERN_INFO "Coursera: Received %zu characters\n", len);
    return len;
}

static struct file_operations fops = {
    .open = dev_open,
    .write = dev_write,
};

static int __init coursera_init(void) {
    printk(KERN_INFO "Coursera Final Assignment 7: Module loaded\n");
    
    major_number = register_chrdev(0, DEVICE_NAME, &fops);
    if (major_number < 0) {
        printk(KERN_ALERT "Failed to register a major number\n");
        return major_number;
    }
    
    printk(KERN_INFO "Coursera: Registered with major number %d\n", major_number);
    return 0;
}

static void __exit coursera_exit(void) {
    unregister_chrdev(major_number, DEVICE_NAME);
    printk(KERN_INFO "Coursera Final Assignment 7: Module unloaded\n");
}

module_init(coursera_init);
module_exit(coursera_exit);

MODULE_LICENSE("GPL");
MODULE_AUTHOR("Azim Darxonov");
MODULE_DESCRIPTION("Final Project for Coursera Embedded Linux Course - Assignment 7");
