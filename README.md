# PHP stringify

```php
<?php declare(strict_types=1);
    /**
     * Crear una versión string de un valor.
     *
     * @param mixed $value
     * @return string
     * @throws Exception
     *
     * @see https://github.com/symfony/validator/blob/7.0/ConstraintValidator.php#L73 Symfony formatValue.
     * @see https://stackoverflow.com/questions/28798159/php-array-stringify stackoverflow stringify.
     * @see  https://github.com/beberlei/assert/blob/60313415c987302b158c47a2914218a8b8441828/lib/Assert/Assertion.php#L2736 beberlei assert stringify.
     */
    protected function stringify(mixed $value): string
    {
        if ($value instanceof DateTimeInterface) {
            if (class_exists(IntlDateFormatter::class)) {

                $timezone = new DateTimeZone(date_default_timezone_get());

                $formatter = new IntlDateFormatter(Locale::getDefault(), IntlDateFormatter::MEDIUM, IntlDateFormatter::LONG, $timezone);

                return $this->stringify(
                    $formatter->format(new DateTimeImmutable(
                        $value->format("Y-m-d H:i:s.vp"),
                        $timezone
                    ))
                );
            }

            return $this->stringify($value->format("Y-m-d H:i:s.vp"));
        }

        if ($value instanceof UnitEnum) {
            return $value->name;
        }

        if (is_object($value)) {
            if (method_exists($value, '__toString')) {
                return $this->stringify($value->__toString());
            }

            return json_encode($value, JSON_PRETTY_PRINT);
        }


        if (is_array($value)) {
            return json_encode($value, JSON_PRETTY_PRINT);
        }

        if (is_string($value)) {
            return '"' . $value . '"';
        }

        if (is_resource($value)) {
            return 'resource;' . get_resource_type($value);
        }

        if (null === $value) {
            return 'null';
        }

        if (false === $value) {
            return 'false';
        }

        if (true === $value) {
            return 'true';
        }

        if (is_scalar($value)) {
            $val = (string)$value;

            if (mb_strlen($val) > 100) {
                return mb_substr($val, 0, 97) . "...";
            }

            return $val;
        }

        return gettype($value);
    }
```
