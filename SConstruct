import pathlib

# Don't swallow any internal exceptions
AllowSubstExceptions()

# Use this directory as build cache
CacheDir(pathlib.Path.home() / 'build_cache')

# Use source and target hashes to decide if rebuild is needed
def source_and_target_decider(dependency, target, prev_ni, repo_node=None):
    src_old_csig = prev_ni.csig if hasattr(prev_ni, 'csig') else None
    src_new_csig = dependency.get_csig()

    tgt_stored_info = target.get_stored_info().ninfo
    tgt_old_csig = tgt_stored_info.csig if hasattr(tgt_stored_info, 'csig') else None
    tgt_new_csig = target.get_csig()

    return src_new_csig != src_old_csig or tgt_new_csig != tgt_old_csig

Decider(source_and_target_decider)

# Use nothing from user environment by default
env = DefaultEnvironment(ENV=dict())
env['RPATH'] = []
env['LIBPATH'] = []
env['LIBS'] = []
env['CPPPATH'] = []
env['CPPFLAGS'] = []
env['CPPDEFINES'] = []
env['LINKCOMSTR'] = '$LINKCOM'
env['CCCOMSTR'] = '$CCCOM'

# Create x86 environment
x86env = env.Clone()
x86env['CC'] = '/usr/bin/gcc'
x86env['ENV'] = {'PATH': ['/usr/bin']}

# Create cortex m4 environment
armenv = env.Clone()
armenv['CC'] = '/usr/bin/arm-none-eabi-gcc'
armenv['ENV'] = {'PATH': ['/usr/bin']}
armenv['CCFLAGS'] = ['-mthumb', '-mcpu=cortex-m4']
armenv['LINKFLAGS'] = '--specs=nosys.specs'

# Targets referenced in folder build_x86 user sources in src
VariantDir('build_x86', 'src', duplicate=False)
# Targets referenced in folder build_arm user sources in src
VariantDir('build_arm', 'src', duplicate=False)

# Build source for x86
programs = SConscript('build_x86/SConscript', exports={'env': x86env})
# Build source for arm
programs += SConscript('build_arm/SConscript', exports={'env': armenv})

# Archive all the executables
top_build_dir = Dir('build_top')
archive = Command(f'{top_build_dir}/foo.tar', programs, f'tar -cf $TARGET $SOURCES')
