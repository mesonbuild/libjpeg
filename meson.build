project('libjpeg', 'c', version : '9a', license : 'bsd-like')

sources = [
  'jaricom.c', 'jcapimin.c', 'jcapistd.c', 'jcarith.c', 'jccoefct.c', 'jccolor.c',
  'jcdctmgr.c', 'jchuff.c', 'jcinit.c', 'jcmainct.c', 'jcmarker.c', 'jcmaster.c',
  'jcomapi.c', 'jcparam.c', 'jcprepct.c', 'jcsample.c', 'jctrans.c', 'jdapimin.c',
  'jdapistd.c', 'jdarith.c', 'jdatadst.c', 'jdatasrc.c', 'jdcoefct.c', 'jdcolor.c',
  'jddctmgr.c', 'jdhuff.c', 'jdinput.c', 'jdmainct.c', 'jdmarker.c', 'jdmaster.c',
  'jdmerge.c', 'jdpostct.c', 'jdsample.c', 'jdtrans.c', 'jerror.c', 'jfdctflt.c',
  'jfdctfst.c', 'jfdctint.c', 'jidctflt.c', 'jidctfst.c', 'jidctint.c', 'jquant1.c',
  'jquant2.c', 'jutils.c', 'jmemmgr.c',
]

memmgr_sources = ['jmemansi.c']

cc = meson.get_compiler('c')
cdata = configuration_data()
cdata.set('HAVE_PROTOTYPES', true)
cdata.set('HAVE_UNSIGNED_CHAR', true)
cdata.set('HAVE_UNSIGNED_SHORT', true)
cdata.set('HAVE_STDDEF_H', true)
cdata.set('HAVE_STDLIB_H', true)
cdata.set('HAVE_LOCALE_H', cc.has_header('locale.h'))
cdata.set('NEED_FAR_POINTERS', false)
cdata.set('NEED_SHORT_EXTERNAL_NAMES', false)

signed_char_code = '''
#include <stdlib.h>
#include <stdio.h>

int is_char_signed (int arg)
{
  if (arg == 189) {		/* expected result for unsigned char */
    return 0;			/* type char is unsigned */
  }
  else if (arg != -67) {	/* expected result for signed char */
    printf("Hmm, it seems 'char' is not eight bits wide on your machine.\n");
    printf("I fear the JPEG software will not work at all.\n\n");
  }
  return 1;			/* assume char is signed otherwise */
}
char signed_char_check = (char) (-67);
int main() {
  exit(is_char_signed((int) signed_char_check));
}
'''

shift_code = '''
#include <stdlib.h>
#include <stdio.h>
int is_shifting_signed (long arg)
/* See whether right-shift on a long is signed or not. */
{
  long res = arg >> 4;

  if (res == -0x7F7E80CL) {	/* expected result for signed shift */
    return 1;			/* right shift is signed */
  }
  /* see if unsigned-shift hack will fix it. */
  /* we can't just test exact value since it depends on width of long... */
  res |= (~0L) << (32-4);
  if (res == -0x7F7E80CL) {	/* expected result now? */
    return 0;			/* right shift is unsigned */
  }
  printf("Right shift isn't acting as I expect it to.\n");
  printf("I fear the JPEG software will not work at all.\n\n");
  return 0;			/* try it with unsigned anyway */
}
int main() {
  exit(is_shifting_signed(-0x7F7E80B1L));
}
'''

cdata.set('CHAR_IS_UNSIGNED', cc.run(signed_char_code).returncode() == 0)
cdata.set('RIGHT_SHIFT_IS_UNSIGNED', cc.run(shift_code).returncode() == 0)

configure_file(input : 'jconfig.h.meson',
  output : 'jconfig.h',
  configuration : cdata)

jpeg = library('jpeg',
  sources, memmgr_sources,
)

cjpeg = executable('cjpeg',
  'cjpeg.c', 'rdppm.c', 'rdgif.c', 'rdtarga.c', 'rdrle.c', 'rdbmp.c',
  'rdswitch.c', 'cdjpeg.c',
  link_with : jpeg
)

djpeg = executable('djpeg',
  'djpeg.c', 'wrppm.c', 'wrgif.c', 'wrtarga.c', 'wrrle.c', 'wrbmp.c',
  'rdcolmap.c', 'cdjpeg.c',
  link_with : jpeg,
)

jpegtran = executable('jpegtran',
  'jpegtran.c', 'rdswitch.c', 'cdjpeg.c', 'transupp.c',
  link_with : jpeg,
)

rdjpgcom = executable('rdjpgcom', 'rdjpgcom.c',
  link_with : jpeg,
)

wrjpgcom = executable('wrjpgcom', 'wrjpgcom.c',
  link_with : jpeg,
)

