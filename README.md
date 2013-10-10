Wordpress---WP-Nav-menu-modify-the-urls-with-language-code
==========================================================

Wordpress - WP Nav menu modify the urls with language code


You can modify the behaviour of wp_nav_menu with the wp_get_nav_menu_items-filter. Here's a somewhat complete example:

    class ModifyLinkFilter {
      protected $_prio = 10;
      protected $_args;
  
      public function __construct($addargs = array(), $prio = 10) {
          $this->_args = $addargs;
          $this->_prio = $prio;
  
          if(!empty($addargs)) {
              $this->register();
          }
      }
  
      public function register() {
          add_filter('wp_get_nav_menu_items',
              array($this, 'on_nav_items'), $this->_prio, 3);
      }
  
      public function unregister() {
          remove_filter('wp_get_nav_menu_items',
              array($this, 'on_nav_items'), $this->_prio, 3);
      }
  
      public function on_nav_items($items, $menu, $args) {
          foreach($items as $item) {
              if(!empty($item->url)) {
                  $item->url = self::modifyUrlSimple($item->url, $this->_args);
              }
          }
          return $items;
      }
  
      public static function modifyUrlSimple($url, $query) {
          $url .= strchr($url, '?') === false ? '?' : '&';
          $url .= http_build_query($query);
          return $url;
      }
    }
  
  
    // You can use the class like that
    $language = "de";
    $args = array('lang' => $language, 'foo' => 'bar');
    $linkfilter = new ModifyLinkFilter($args); 
    wp_nav_menu();
    $linkfilter->unregister();    
This modifies every item in the navigation menu. So if you have an external link, it will be changed as well.

Plus, modifying the URL is not as easy as it seems. The URL of an item can be /blabla?myvalue=5#anchor which would look like /blabla?myvalue=5#anchor&lang=de&foo=bar after the modification.
