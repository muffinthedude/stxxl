project('stxxl', 'cpp',
    version: '1.4.99',
    default_options : [
        'cpp_std=c++17', 
        'warning_level=3',
        'werror=true',
    ])

# Compiler object
cc = meson.get_compiler('c')
configuration = configuration_data()

# Get the current refspec (branch name or tag)
git_refspec = run_command('git', 'rev-parse', '--abbrev-ref', 'HEAD').stdout().strip()
configuration.set('STXXL_VERSION_GIT_REFSPEC', git_refspec)

# Get the current commit SHA
git_sha1 = run_command('git', 'rev-parse', 'HEAD').stdout().strip()
configuration.set('STXXL_VERSION_GIT_SHA1', git_sha1)

configuration.set('STXXL_DEBUG_ASSERTIONS', 0)

configuration.set('STXXL_DIRECT_IO_OFF', 0)
if cc.has_function('mmap', prefix: '#include <sys/mman.h>')
  configuration.set('STXXL_HAVE_MMAP_FILE', '1')
else
  configuration.set('STXXL_HAVE_MMAP_FILE', '0')
endif

# Define the code snippet you want to test
test_code = '''
#include <unistd.h>
#include <sys/syscall.h>
#include <linux/aio_abi.h>
int main() {
    aio_context_t context;
    long r = syscall(SYS_io_setup, 5, &context);
    return (r == 0) ? 0 : -1;
}
'''
# Check if the code snippet compiles
if cc.compiles(test_code, name: 'linux_aio_test')
  configuration.set('STXXL_HAVE_LINUXAIO_FILE', '1')
else
  configuration.set('STXXL_HAVE_LINUXAIO_FILE', '0')
endif

configuration.set('STXXL_POSIX_THREADS', '0')
configuration.set('STXXL_STD_THREADS', '1')
configuration.set('STXXL_WINDOWS', '0')
configuration.set('STXXL_MSVC', '0')

cpp = meson.get_compiler('cpp')

test_code = '''
#include <vector>
int main() { std::vector<int> v(42); for (auto i : v) { ++i; } return 0; }
'''

if cpp.compiles(test_code, name : 'test_STXXL_HAVE_CXX11_RANGE_FOR_LOOP')
  configuration.set('STXXL_HAVE_CXX11_RANGE_FOR_LOOP', '1')
  message('C++11 range-based for loop is supported.')
else
  configuration.set('STXXL_HAVE_CXX11_RANGE_FOR_LOOP', '0')
  message('C++11 range-based for loop is not supported.')
endif

test_code = '''
int main() { int x; __sync_add_and_fetch(&x, +1); return 0; }
'''

if cpp.compiles(test_code, name : 'test_STXXL_HAVE_SYNC_ADD_AND_FETCH')
  configuration.set('STXXL_HAVE_SYNC_ADD_AND_FETCH', '1')
  message('Atomic built-in function __sync_add_and_fetch is supported.')
else
  configuration.set('STXXL_HAVE_SYNC_ADD_AND_FETCH', '0')
  message('Atomic built-in function __sync_add_and_fetch is not supported.')
endif

configuration.set('STXXL_PARALLEL', '1')
configuration.set('STXXL_WITH_GNU_PARALLEL', '0')
configuration.set('STXXL_STD_RANDOM', '1')
configuration.set('STXXL_WITH_VALGRIND', '0')

test_code_mallinfo = '''
#include <malloc.h>
int main() { struct mallinfo mi = mallinfo(); return 0; }
'''

test_code_mlock = '''
#include <sys/mman.h>
int main() { mlock(nullptr, 0); return 0; }
'''

if cpp.compiles(test_code_mallinfo, name : 'test_STXXL_HAVE_MALLINFO_PROTO')
  configuration.set('STXXL_HAVE_MALLINFO_PROTO', '1')
  message('Function mallinfo is supported.')
else
  configuration.set('STXXL_HAVE_MALLINFO_PROTO', '0')
  message('Function mallinfo is not supported.')
endif

if cpp.compiles(test_code_mlock, name : 'test_STXXL_HAVE_MLOCK_PROTO')
  configuration.set('STXXL_HAVE_MLOCK_PROTO', '1')
  message('Function mlock is supported.')
else
  configuration.set('STXXL_HAVE_MLOCK_PROTO', '0')
  message('Function mlock is not supported.')
endif

# Boost
configuration.set('STXXL_BOOST_CONFIG', '0')
configuration.set('STXXL_BOOST_FILESYSTEM', '0')
configuration.set('STXXL_BOOST_RANDOM', '0')
configuration.set('STXXL_BOOST_THREADS', '0')
configuration.set('STXXL_BOOST_TIMESTAMP', '0')

subdir('include/stxxl/bits')

incdir = include_directories('include', 'include/stxxl', 'include/stxxl/bits')
stxxl_dep = declare_dependency(
    include_directories: incdir
)